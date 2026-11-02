# 011: Integer literals and compile-time realization

| Field | Value |
| --- | --- |
| Status | Historical project record / non-normative / audit-only |
| Work Item | `011` |
| Created | 2026-08-31 |
| Completed | 2026-09-01 |
| Owns | Historical evidence and decisions from the bounded review defined by the fixed initiating input |
| Does Not Own | Current language behavior, complete non-integer literal families, or internal compiler mechanisms |

## Non-authority notice

This archived file is a historical audit record, not a current language owner.
Its accepted findings were incorporated into current language documentation
through the promotion records below. Candidate discussion, superseded analysis,
and discovery chronology remain non-authoritative.

## Fixed initiating input

This section records the information aligned when work item `011` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for integer literals and
compile-time integer realization:

1. how integer source produces a mathematical/compiler-known value;
2. decimal, binary, octal, hexadecimal, and other accepted integer bases;
3. attached prefix forms such as `h'...'`, where retained;
4. positive, unsigned-requested, and negative source;
5. whether a sign participates in realization or applies an operator afterward;
6. unconstrained default realization as `Integer` or `UInteger`;
7. direct realization against a typed slot;
8. compiler-known uncommitted integer values or an equivalent source model;
9. arbitrary-precision compile-time evaluation before concrete realization;
10. exact fit, overflow, and underflow;
11. the minimum signed value without requiring an impossible positive
    intermediate;
12. narrow expected-result context and its limits;
13. interaction with exact, optional, and narrowing integer conversion;
14. active, target, and compiler-host type requests;
15. costs, failures, diagnostics, formatting, and source stability; and
16. lasting documentation owners and boundaries.

Recover Zax-specific intent before adopting another language's literal rules.

### Motivating pressure

Current integer teaching relies on ordinary source such as:

```zax
myValue := 56
myUnsigned := +56
myNegative := -27
myByte : Byte = 255
myMinimum : I8 = -128
```

Programmers need to predict which type each expression receives, when a typed
slot realizes the value directly, and when compilation rejects a value that does
not fit.

Current design also allows a compile-time expression to supply a concrete type:

```zax
// Illustrative type-producing expression; exact generic syntax remains future.
MySelectedType :: alias type chooseIntegerType()
myValue : MySelectedType = 42
```

This work must explain the programmer-visible result without describing internal
compiler communication.

### Known assumptions

- [Zax integers](../../../language/integers.md) owns current integer types, ranges,
  identities, storage, and conversion relationships.
- [Zax integer operator catalog](../../../language/integer-operator-catalog.md)
  owns protected signedness, negation, and conversion behavior.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  owns typed and inferred declaration forms and direct initialization.
- [Zax function invocation](../../../language/function-invocation.md) owns narrow
  expected-result context and result selection.
- [Zax source structure](../../../language/source-structure.md) owns token,
  whitespace, quote, and continuation boundaries already established.
- [Zax operator catalog](../../../language/operator-catalog.md) owns exact operator
  forms and precedence.
- Every successfully selected single expression result ultimately has one
  concrete static type.
- A typed slot may directly realize an uncommitted compile-time integer value;
  this is not implicit conversion from a hidden preselected `Integer`.
- An expression already committed to `Integer` does not gain another conversion
  merely because it is constant.
- There is no language implementation in this repository.

### Programmer-visible compile-time boundary

A type-producing compile-time expression may supply the concrete type used to
realize an integer literal. This work accepts that capability at the
programmer-visible level.

The required model is only:

1. the compile-time expression supplies a concrete type identity;
2. that identity is available before the typed initializer is realized;
3. the integer value realizes directly against that type; and
4. an unrepresentable value produces a compiler diagnostic.

Do not design or explain:

- internal type-metadata representation;
- compiler-generated bridge code;
- reflection metadata transport;
- compiler-host ABI communication;
- compiler callbacks;
- lowering or evaluation-engine architecture; or
- internal compiler phase/data-structure mechanics.

If a concrete source-level contradiction depends on one of those mechanisms,
capture the pressure for implementation/future specification work without
solving it here.

### Known inclusions

- Default type and signedness for unconstrained integer source.
- Typed-slot and expected-result realization.
- Compile-time-known uncommitted integer values.
- Positive source, pre-unary unsigned request, and negative source.
- Minimum signed values.
- Exact-value fit and compile-time failure.
- Supported bases, prefixes, digits, separators, and readability.
- Grouping that must not change type, value, width, or failure.
- Interaction with exact, optional, and narrowing integer conversion.
- Environment-selected integer identities at the depth required to realize a
  literal.
- Source stability when defaults, prefixes, or type context change.
- Diagnostics that show requested type, value, range, and deciding context.
- Documentation fit and lasting ownership.

### Known starting boundaries

- Complete floating-point rounding and literal representation.
- Complete fixed-point, decimal, and unbounded numeric literals.
- Complete string, character, byte-string, and encoding literal behavior.
- General user-defined literal operators.
- Complete compile-time execution, scheduling, sandboxing, and resource policy.
- Complete generic and computed-type syntax.
- Complete reflection and metadata representation.
- Formal grammar, compiler implementation, lowering, or ABI bridges.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Whether an uncommitted integer value is a compile-time-only type, another
  static category, or an equivalent result mechanism.
- Whether sign is part of literal source formation or a selected unary operation
  in every applicable context.
- Exact base and prefix spellings.
- Digit separators and grouping rules.
- Default width growth when a value exceeds the ordinary `Integer` range.
- Whether arbitrary-precision compile-time integers always remain available.
- How explicit target/compiler-host type requests constrain realization.
- Exact diagnostics and formatting.
- Which findings belong in an integer owner, literal owner, source structure,
  operator catalog, compile-time owner, or generic/reflection future input.

### Initial stopping guidance

Stop when the work has:

- established how ordinary integer source obtains a concrete type;
- established typed-slot direct realization without implicit conversion;
- established positive, unsigned-requested, negative, and minimum-signed source;
- established bases, prefixes, digit rules, and fit diagnostics at
  human-developer depth;
- reconciled grouping, expected-result context, conversion, and environment
  selection;
- preserved compile-time/generic/reflection implementation pressure without
  designing internal machinery;
- assigned every deferred consequence a live owner and activation pressure;
- identified lasting owners and an exact promotion set; and
- completed the required documentation-fit dry run.

Do not promote findings, design compiler internals, archive this work item, or
begin work item `012` without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs focused reading,
  numbered work, teachability, dry runs, promotion, and archival.
- Focused [Zax integers](../../../language/integers.md#initialization-and-compile-time-realization) -
  provides the current direct-realization boundary.
- Focused [integer operator catalog](../../../language/integer-operator-catalog.md#signedness-counterpart) -
  provides unary signedness and conversion constraints.
- Focused [declarations and bindings](../../../language/declarations-and-bindings.md#core-value-forms) -
  provides typed/inferred declaration and direct-initialization behavior.
- Focused [function invocation](../../../language/function-invocation.md#narrow-expected-result-selection) -
  provides the accepted expected-result boundary.
- Focused [operator catalog](../../../language/operator-catalog.md#fixity-and-adjacency) -
  provides unary/operator source formation.
- Focused [source structure](../../../language/source-structure.md#phrase-fences-and-literal-coordination) -
  provides quote attachment and phrase-fence boundaries.
- [Raw literal input](../../raw/literal-operators.md) - preserves unresolved
  realization, prefix, payload, and custom-literal pressure.
- [Raw compile-time execution input](../../raw/compile-time-execution.md) -
  preserves the programmer-visible execution-context and type-result boundary.
- Focused [legacy intrinsic system literals](../../../basics.md#intrinsic-system-literals) -
  preserves detailed candidate bases, prefixes, separators, and literal
  examples not yet consumed.

### Consequence-driven

- Read [raw type-parameter and generic input](../../raw/type-parameters-and-generics.md)
  only when a concrete source-level type result cannot be described without it.
- Read [raw CPU-provider input](../../raw/cpu-provider-model.md) only when
  active/target/compiler-host realization cannot be resolved from current
  integer contracts.
- Read [raw reflection input](../../raw/reflection.md) only when literal provenance
  or realization must be observable to source/tooling.
- Read [raw numeric-family input](../../raw/numeric-type-families.md) when an
  unusual width or arbitrary-precision value constrains current literal
  behavior.
- Read floating, fixed-point, string, or custom-literal input only when a shared
  source decision would otherwise contradict those future families.
- Read focused legacy compiler directives, meta-functions, or meta-types only
  when a concrete programmer-visible contradiction requires their evidence.
  Do not read them merely to explain how the compiler transports or returns a
  type identity.

### Audit-only

- `project/archive/`, including work items `001` through `010`.
- Superseded implementation sketches and compiler-internal explanations.

Do not read archived work item `010` during ordinary work on `011`. Its current
findings are promoted into language owners and its future pressures are indexed
in raw inputs.

## Superseded initial reconstruction

> This initial evidence-based reconstruction is retained as working history. It
> has been superseded by the reviewed [working record](#working-record) below.
> When the two sections differ, the later working record represents the current
> aligned understanding or explicitly marked tentative proposal.

> **Maturity of this section:** Everything below is an initial
> evidence-based reconstruction for review. It is raw input or candidate
> analysis, not an aligned finding or accepted language design.

### Review entry point

The strongest candidate model is that integer source first denotes an exact
mathematical integer without yet choosing storage, logical width, or a concrete
integer identity. A direct typed boundary can realize that value as the requested
integer type when it fits. Without such a boundary, ordinary positive and
negative source defaults to the active `Integer`, while pre-unary `+` requests
the active `UInteger`.

That model explains all three currently taught forms without inventing an
implicit conversion:

```zax
myValue := 56              // candidate: Integer
myUnsigned := +56          // candidate: UInteger
myNegative := -27          // candidate: Integer
myByte : Byte = 255        // direct realization as Byte
myMinimum : I8 = -128      // direct realization of mathematical -128
```

The most important unresolved boundary is not the spelling of a base prefix. It
is which expressions may remain uncommitted mathematical integers. A literal
clearly can. The existing `compileTimeResult()` examples require at least some
compile-time operation results to do so. It is not yet clear whether an entire
expression such as `200 + 55` can remain exact and uncommitted until a surrounding
`U8` slot realizes it, or whether concrete operator selection must commit its
operands earlier.

That decision controls intermediate overflow, overload selection, function
arguments, conversions, the meaning of nested unary signs, and how much
arbitrary-precision evaluation programmers can rely on. It should be reviewed
before exact prefix and separator spelling.

Two additional tensions need early review:

1. Pre-unary `+` means "request unsigned realization" for an uncommitted literal,
   but means "request the equal-width opposite-signedness counterpart" for a
   concrete integer. This can be a coherent overload by operand category, but
   the transition from uncommitted to concrete must be visible and predictable.
2. Legacy `h'...'` examples use the same attached-prefix shape both as a number
   and as an element merged into a character sequence. Integer work can define
   the mathematical value of a numeric payload, but it should not silently
   accept the legacy merged-literal model or let surrounding text change a
   numeric literal after the fact.

### Reconstructed intent from the current evidence

The current owners and raw inputs consistently support these parts of the
design:

- An explicitly typed declaration performs direct initialization. It does not
  default-initialize and assign later.
- A typed integer slot can directly realize an uncommitted literal or
  compile-time integer result. An out-of-range value is a compile-time error.
- Direct realization is not conversion from a hidden `Integer`.
- Constancy and type commitment are independent. A constant expression already
  typed as `Integer` remains `Integer`.
- Every successfully selected expression result ultimately has one concrete
  static type. An uncommitted integer therefore cannot be a runtime value that
  escapes realization.
- Integer identities remain distinct. Equal representation in one profile does
  not create implicit conversion.
- Exact, optional, and narrowing conversions are distinct explicit policies.
- `Integer`, `Target.Integer`, and `CompilerHost.Integer` name active,
  target-selected, and compiler-host-selected roles. Exact types such as `I32`
  pin their range independently of those role selections.
- An attached single quote begins a literal payload, while a
  whitespace-separated single quote begins an exact phrase fence. Integer
  literal work cannot reinterpret that boundary.
- Grouping must not change an integer source value's type request, value, width,
  or failure.

The legacy base examples are evidence rather than accepted behavior:

```zax
binary := b'1011101'
octal := o'12345670'
duodecimal := d'1234567890AB'
hexadecimal := h'ABC123'
```

They establish concrete pressure for bases 2, 8, 12, and 16 and for attached
prefix payloads. They do not establish a complete prefix catalog, digit case,
separator syntax, empty-payload behavior, or whether these names are reserved,
imported, overloadable, or user-definable.

### Candidate value and realization model

#### Three programmer-visible states

A useful explanation appears to require three states:

1. **Integer source value.** Decimal digits or an accepted numeric prefix and
   payload denote a nonnegative mathematical integer. The radix changes how
   digits are read, not the resulting value's width or type.
2. **Uncommitted compile-time integer result.** Unary sign handling and approved
   compile-time operations may produce another exact mathematical integer while
   no concrete integer identity has been selected. This is a compile-time-only
   result category or equivalent behavior, not necessarily a source-nameable
   type.
3. **Concrete integer value.** A realization boundary chooses one exact or
   role-selected integer identity. Ordinary integer operations and explicit
   conversions then follow that identity's finite range and protected-operation
   rules.

This is a programmer model, not a proposed compiler representation. A compiler
may implement it however it chooses as long as the distinctions and diagnostics
remain observable as described.

An uncommitted result should carry at least:

- its exact mathematical value;
- whether source requested unsigned realization;
- enough source provenance for a useful diagnostic, without making spelling
  part of type identity; and
- whether it is still eligible for direct realization.

It should not carry an inferred storage width from its digit count, radix,
leading zeros, or separator grouping.

#### Direct realization

A direct, complete typed boundary supplies the destination identity before the
uncommitted value is realized:

```zax
myByte : Byte = 255
mySigned : I8 = 127
myMinimum : I8 = -128
myTarget : Target.Integer = 42
```

The candidate rule is:

1. evaluate the uncommitted integer result exactly;
2. obtain the concrete destination identity, including its selected environment
   and range;
3. check the exact mathematical value against that range; and
4. either create that concrete value directly or diagnose failure.

```zax
myTooPositive : I8 = 128 // error: 128 is outside I8 -128..127
myTooNegative : I8 = -129 // error: -129 is outside I8 -128..127
myNegativeUnsigned : U8 = -1 // error: -1 is outside U8 0..255
myTooWide : Byte = 256 // error: 256 is outside Byte 0..255
```

No temporary `Integer`, conversion operator, runtime range check, or modular
truncation is implied.

A compile-time expression may also provide the destination type:

```zax
// Illustrative type-producing expression; generic syntax remains future work.
MySelectedType :: alias type chooseIntegerType()
myValue : MySelectedType = 42
```

The source-level requirement is only that `MySelectedType` resolves to one
concrete integer identity before `42` is realized. The mechanism by which the
compiler obtains or transports that identity is outside this work.

#### Unconstrained defaults

The evidence directly supports:

```zax
myValue := 56     // Integer
myUnsigned := +56 // UInteger
myNegative := -27 // Integer
```

The safest candidate for an out-of-range unconstrained value is to diagnose it
rather than silently select a wider family:

```zax
myValue := 999999999999999999999999999999999999
// candidate error when the value does not fit active Integer;
// state I128, another exact width, or another destination deliberately
```

Silent width growth would make the inferred identity depend on the value and
could make small source edits change overloads, storage, ABI, or operation cost.
Selecting the first wider profile role would also be target-dependent. Keeping a
single default preserves the existing claim that inferred decimal `0` is
`Integer`.

This no-growth rule is only a candidate. The maintainer still needs to decide
whether a large unconstrained value:

- fails and asks for an explicit destination;
- selects the smallest predefined exact type that fits;
- realizes a deliberately named maximum family; or
- exposes an unbounded compile-time integer type.

The last three choices would materially expand the type-inference and
source-stability model.

### Unary signs and the minimum signed value

The cleanest source model is that the integer token or attached numeric payload
contains no sign. Pre-unary operators act on the uncommitted value before
concrete realization when their direct operand remains uncommitted.

For `-`, this means mathematical negation happens before the fit check:

```zax
myMinimum : I8 = -128  // valid candidate: realize mathematical -128 as I8
myPositive : I8 = 128  // error: mathematical 128 does not fit I8
myGrouped : I8 = -(128) // same value and result as -128
```

There is never an intermediate `I8(128)`, so the minimum signed value does not
depend on an impossible positive value.

Once the operand is concrete, the current protected negation rules apply
instead:

```zax
myMinimum : I8 = -128
myAgain := -myMinimum
// protected I8 negation: panic, or a compiler diagnostic when evaluated at
// compile time
```

For `+`, the candidate distinction is:

```zax
myUnsigned := +55
// uncommitted operand: request unsigned realization, then default to UInteger

myU8 : U8 = 55
myI8 := +myU8
// concrete operand: request the equal-width signed counterpart I8
```

Grouping does not affect the request:

```zax
myA := +55
myB := +(55) // same type, value, and failure behavior as myA
```

The following cases remain decisions rather than reconstructed rules:

```zax
mySigned : I8 = +42
myUnsigned : U8 = 42
myNested : I8 = -(+128)
myOther := +(-1)
```

The main candidate is that explicit `+` is a real unsigned constraint, not merely
an unconstrained default hint. Under that model `I8 = +42` is a signedness
conflict, while bare `42` may realize as either a signed or unsigned destination.
Nested signs need a rule that does not depend on arbitrary grouping or a hidden
early realization. If this model makes common generic or constant code awkward,
the alternative is for `+` to choose only the unconstrained default and yield to
an explicit destination. That alternative weakens the meaning of "requests
unsigned realization" and needs equally explicit wording.

The longest-token rule remains unchanged. Where `--` is a recognized token,
separate unary negations require grouping:

```zax
-(-1)
```

### Radix and attached-prefix candidates

The candidate semantic split is:

- unprefixed digit source is decimal;
- an accepted attached numeric prefix selects a radix-specific digit parser;
- the parser produces the same kind of nonnegative mathematical integer as
  decimal source;
- a prefix does not select a logical width, signedness, or concrete integer
  identity; and
- leading zeros and visual grouping do not change value category or type.

Under that model, legacy forms can be evaluated consistently:

```zax
myDecimal : U8 = 255
myBinary : U8 = b'11111111'
myOctal : U8 = o'377'
myHex : U8 = h'FF'
myMinimum : I8 = -h'80'

myTooLarge : U8 = h'100' // error: mathematical 256 does not fit U8
myBadDigit := b'102'      // error: digit 2 is invalid for base 2
```

These are candidate examples, not accepted grammar. In particular:

- `b`, `o`, `d`, and `h` have legacy evidence, but `d` means duodecimal rather
  than the more obvious decimal and needs deliberate review.
- The legacy examples do not establish whether alphabetic digits are
  case-insensitive.
- The required-reading description says the legacy section preserves separator
  candidates, but neither that section nor the other required material actually
  supplies a digit-separator spelling or grouping rule.
- No evidence establishes whether empty numeric payloads, leading/trailing
  separators, repeated separators, or whitespace inside a payload are accepted.
- A sign inside the quoted payload would duplicate unary sign syntax. The
  candidate is to keep signs outside (`-h'80'`) and accept only radix digits and
  an eventually selected separator inside.

The quote boundary itself is not open:

```zax
h'FF'    // attached payload candidate
foo 'bar' // exact phrase fence, never an integer payload passed to foo
```

Numeric prefixes still need a namespace and extension decision. A closed
language-provided radix catalog gives the strongest source stability. An open
literal-operator catalog supports forms such as `roman'IX'` but makes lookup,
shadowing, imports, result selection, and compile-time availability part of
literal meaning. A bounded model could reserve core numeric prefixes and allow
separately marked custom prefixes. The integer value model does not require this
extension decision to be solved, but promotion cannot present `h'...'` as stable
syntax until its recognition rule is settled.

### Exact compile-time evaluation and commitment

An uncommitted integer is exact rather than a hidden fixed-width machine value.
Therefore it has no arithmetic overflow before realization. This is distinct
from promising an unbounded runtime integer type.

The difficult question is which operations preserve that category. A useful
candidate is:

- literal parsing and unary mathematical sign handling preserve it;
- a compile-time function may explicitly return it through a future
  programmer-visible contract;
- language-defined operations may preserve it only when their contract is
  defined over mathematical integers without relying on a concrete logical
  width or identity; and
- encountering a concrete integer operand or an explicit realization boundary
  commits the applicable result.

This would make the following a useful test case:

```zax
myByte : U8 = 200 + 55 // candidate: exact 255, then direct U8 realization
myOverflow : U8 = 200 + 56
// candidate error: exact mathematical result 256 is outside U8
```

It must not be accepted merely by assertion. Current integer operators are
defined for same-identity concrete operands and retain that identity. Supporting
the example above therefore needs either:

1. a deliberately defined uncommitted arithmetic domain for selected
   compile-time operations; or
2. a rule by which the complete typed boundary selects `U8` operator operands
   before evaluation.

The first approach better explains arbitrary-precision evaluation and avoids
intermediate finite-width overflow. The second reuses concrete operators but
makes the surrounding result type flow inward and could contradict the narrow
expected-result rule. This is the central design choice for the next review.

Not every integer-looking operation can automatically remain uncommitted.
Bitwise complement, rotation, wrapping behavior, and some shifts require a
logical width. Division and remainder require an agreed signed rounding rule.
Overloaded user operations require concrete candidate selection. The eventual
model needs an explicit list or general criterion rather than "the compiler
constant-folds it."

Arbitrary precision here means exact mathematical semantics until realization,
subject to separately defined compile-time resource limits. A resource-limit
diagnostic is not integer overflow and must not silently substitute a concrete
width. The current finite language maximum constrains realizable integer types;
whether it also caps the magnitude or bit length of an uncommitted temporary is
still open.

### Expected context and where it stops

Current invocation design permits expected-result selection only at a direct,
complete, explicitly typed boundary. The integer model should reuse that narrow
shape rather than allow later use to reach backward through arbitrary
expressions.

Clear direct cases include:

```zax
myByte : Byte = 255
myCompileTimeByte : Byte = compileTimeResult()

makeByte : (result : Byte)() = {
  return 255
}
```

An inferred declaration supplies no destination type:

```zax
myValue := 255 // default realization is required
```

An already committed value does not reopen:

```zax
myInteger : Integer = 255
myByte : Byte = myInteger // error: distinct integer identities
```

The candidate stopping rule is that a direct typed boundary may consume a whole
uncommitted integer result, but it cannot propagate through a committed
subexpression, choose between source structures, or create an implicit
conversion. This still leaves several important holes:

- Does ordinary assignment provide a realization destination, or does its
  source default before the assignment operator is selected?
- Does a function parameter provide direct realization for an argument such as
  `consume(255)`, and how does that interact with overload ambiguity?
- Can an outer typed declaration select concrete operators for `1 + 2`, or only
  realize an expression that independently remained uncommitted?
- What source-level function result contract permits `compileTimeResult()` to
  return an uncommitted integer rather than `Integer`?
- Which complete multiple-result and conditional-expression paths may carry an
  uncommitted result before convergence?

These are not minor elaborations. Without answers, ordinary call ergonomics and
overload stability cannot be predicted.

### Realization versus conversion

Direct realization accepts only an exact fit. It has no optional, saturating,
wrapping, or narrowing variant hidden inside it.

Once an explicit conversion form is written, its stated policy controls:

```zax
myExact := 255 as U8
myAbsent := 256 as U8?
myWrapped := 256 narrowing as U8

// candidate results:
// myExact is U8(255)
// myAbsent is absent
// myWrapped is U8(0)
```

This requires explicit confirmation that an uncommitted integer may be the
source of the current exact/optional/narrowing intrinsic conversion forms. If
confirmed:

- `as U8` is an explicit realization boundary and requires exact fit;
- `as U8?` tests exact fit and returns absence without first committing to
  `Integer`; and
- `narrowing as U8` applies mathematical modulo `2^8`.

Role identities remain separate. A typed `Byte` slot can directly realize a
literal as `Byte`, but an already concrete `U8` or `Integer` needs the role's
declared admission or bridge:

```zax
myByte : Byte = 255

myInteger : Integer = 255
myOtherByte : Byte = myInteger // error: no implicit role conversion
```

No form becomes available merely because a profile currently represents two
identities with the same intrinsic specialization.

### Active, target, and compiler-host realization

A profile-selected destination must be known before fit is checked:

```zax
myActive : Integer = 42
myTarget : Target.Integer = 42
myCompilerHost : CompilerHost.Integer = 42
myPortable : I32 = 42
```

The candidate rule is:

- `Integer` and `UInteger` use the active execution environment;
- `Target.Integer` and `Target.UInteger` use the selected target profile;
- `CompilerHost.Integer` and `CompilerHost.UInteger` use the
  compiler-running-machine profile; and
- exact-width identities use their fixed language ranges.

The compile-time execution owner still has to decide which environment is
active for a given evaluation. Integer realization should not guess
`CompilerHost` merely because the compiler performs the work. If the active
environment is not established, realization of an active profile-selected type
needs a diagnostic or an explicit environment rather than an undocumented host
fallback.

The same mathematical source may therefore fit one profile-selected destination
and fail another. Diagnostics should name the public role, selected environment,
exact range, and relevant profile. Programmers who require one range across
environments use an exact-width identity or an explicit environment path.

A type-producing compile-time expression may select any concrete identity in
this family, including an environment-qualified one. It does not get to return
an unresolved selector and postpone environment choice past realization unless
future generic/CPU-provider work deliberately defines such a contract.

### Cost, failure, diagnostics, and formatting

Literal parsing and direct realization introduce no runtime conversion or range
check. Compile-time evaluation can still have material compiler cost, especially
for very large values. Operations after realization inherit the selected type's
native or software-emulated costs.

Diagnostics need to distinguish:

1. lexical or payload failure, such as a digit invalid for the selected radix;
2. an unknown, unavailable, or ambiguous prefix;
3. a conflicting signedness request;
4. exact-fit failure at realization;
5. use of an already concrete incompatible integer identity;
6. explicit optional or narrowing conversion outcome;
7. unavailable or incomplete environment/profile selection; and
8. compile-time evaluation failure or resource exhaustion.

A fit diagnostic should show the source value, requested public type, its exact
range, the boundary that requested it, and the selected environment/profile when
relevant:

```text
cannot realize integer value 256 from h'100' as U8
U8 represents 0..255; destination requested by declaration of myByte
```

For a minimum signed value, the diagnostic must use the final mathematical value
rather than report failure to create a nonexistent positive intermediate.

Formatting remains open. At minimum, a formatter must not change radix, prefix,
digit spelling, or grouping when doing so could hide intent or change prefix
lookup. No separator convention can be normalized until a separator syntax and
its allowed positions are established.

### Contradictions and holes requiring refinement

1. **Uncommitted-expression extent.** Existing material requires more than a
   literal token but does not say which operators or calls preserve the
   uncommitted result.
2. **Dual use of pre-unary `+`.** Literal unsigned request and concrete
   opposite-signedness operation are distinguishable only if commitment is
   unambiguous.
3. **Expected context versus inward selection.** Typed direct realization is
   accepted, while general result context may not manufacture inner choices.
   Compound integer expressions need a rule consistent with both.
4. **Oversized unconstrained source.** Automatic growth, maximum-family
   selection, unbounded typing, and compile-time failure have materially
   different stability and cost consequences.
5. **Numeric prefix recognition.** Legacy built-ins appear alongside an
   extensible literal-operator idea, but their namespace, reservation, lookup,
   and shadowing model is absent.
6. **Prefix result family.** Legacy `h'...'` is both a numeric source and a
   sequence element. Integer work must not accidentally accept implicit literal
   merging or result-type-directed reinterpretation.
7. **Digit separators.** The initiating input requires a separator decision, but
   the required evidence supplies no actual spelling. A proposal must be
   justified rather than described as recovered design.
8. **Calls and assignment.** Direct parameter/assignment realization is not
   established, yet omitting it could make common literal use require explicit
   wrappers or conversions.
9. **Compile-time result contract.** Examples assume an operation can return an
   uncommitted integer, but no source-level declaration contract currently says
   how.
10. **Arbitrary precision versus the language maximum.** Exact temporary
    evaluation, maximum realizable width, and resource policy are three separate
    limits and must not be collapsed.

### Decisions for maintainer review

The review can proceed in this dependency order:

1. Confirm, revise, or reject the three-state model: source value, uncommitted
   compile-time integer result, and concrete integer value.
2. Decide whether `+` is a binding unsigned constraint or only the
   unconstrained-default selector, and confirm that `-` negates before
   realization for an uncommitted operand.
3. Define the operations that may preserve an uncommitted result, using
   `myByte : U8 = 200 + 55` as the first deciding example.
4. Decide what an oversized unconstrained value does.
5. Define which direct contexts can realize an uncommitted result, especially
   function arguments and ordinary assignment.
6. Decide the core numeric prefix catalog and recognition model before choosing
   digit case and separator details.
7. Confirm how exact, optional, and narrowing conversions consume an
   uncommitted integer.

### Adjacent findings captured for deliberate deferral

| Deferred concern | Live destination and activation pressure | Constraint on this work |
| --- | --- | --- |
| Compile-time scheduling, availability, allocation, recursion, and resource policy | `project/raw/compile-time-execution.md`; activate when compile-time execution is reviewed | Integer work may promise exact value semantics but not unlimited compiler resources or a particular execution engine. |
| Source syntax for type-producing functions and generic integer results | `project/raw/type-parameters-and-generics.md`; activate when a source-level type/result contract is required | A concrete type identity must be available before realization; this work does not invent generic syntax. |
| CPU-profile format, versioning, and host/target coexistence | `project/raw/cpu-provider-model.md`; activate when profile selection or environment mapping is reviewed | Fit against a role uses its selected concrete range, and active must not silently mean compiler host. |
| Runtime unbounded integers, extreme finite widths, and the relation between temporary precision and the language maximum | `project/raw/numeric-type-families.md`; activate for arbitrary-width or unbounded numeric design | An exact uncommitted compile-time value does not itself create a runtime unbounded integer type. |
| General custom literal operators, string/character payloads, escaping, adjacency, and merged sequences | `project/raw/literal-operators.md`; activate for non-core prefixes or non-integer payloads | Attached versus separated quote behavior is fixed; integer prefix decisions must not silently accept merged literals. |
| Reflection or tooling exposure of literal spelling and realization provenance | `project/raw/reflection.md`; activate only if source must observe more than the final value/type or diagnostics need a stable reflective contract | This candidate keeps spelling out of integer type identity and does not require reflective provenance. |
| Floating, fixed-point, decimal, and byte-string literal families | Existing future numeric/literal inputs; activate when a shared prefix or payload rule would constrain them | Core integer syntax should avoid claiming all prefixes or quoted payloads for integers without checking the affected family. |

### Likely lasting ownership boundaries

This is an initial ownership hypothesis, not a dry run or promotion plan:

- A cohesive integer-literal owner is likely needed for the mathematical value,
  radix forms, sign handling, defaults, direct realization, fit, examples, and
  diagnostics. Spreading that teaching across type, source, operator, and
  compile-time pages would force readers to reconstruct the feature.
- `language/integers.md` should retain concrete integer identities, ranges,
  profile-selected roles, and a short direct-realization boundary linked to the
  literal owner.
- `language/source-structure.md` should retain token attachment, quote/fence
  distinction, and eventually the lexical parts of digit and separator syntax.
- `language/operator-catalog.md` and
  `language/integer-operator-catalog.md` should retain unary fixity and the
  concrete integer meanings of signedness, negation, and conversions, with a
  concise handoff for uncommitted operands.
- `language/function-invocation.md` should retain the general narrow
  expected-result rule. The literal owner should explain its integer-specific
  application without redefining callable selection.
- A future compile-time owner should define how an operation promises an
  uncommitted integer result and which evaluation contexts are active. It should
  not own ordinary literal syntax or integer ranges.
- The relevant `basics.md` legacy material should eventually be consumed by
  value: promote supported numeric evidence, preserve unresolved string/custom
  literal evidence in its live raw destination, and remove superseded teaching.

No lasting owner, raw input, router, legacy page, or archive has been changed by
this reconstruction.

## Working record

> **Maturity of this section:** Sections marked **Aligned finding** record the
> language maintainer's current agreement for this work item. They remain
> non-authoritative until separately promoted.

### Current review entry point

An ordinary unprefixed numeric literal begins as an **uncommitted integer**: a
compiler-known mathematical value with unknown, signed, or unsigned intent but
without a concrete integer identity or logical width. A closed family of
width-independent operations may produce another uncommitted integer.

A **commitment boundary** selects one concrete integer type. **Integer
realization** checks sign intent and range, then creates the value directly in
that type. Once committed, a value never becomes uncommitted again merely
because it remains compiler-known.

```zax
myDefault := 55                 // unknown intent commits to Integer
myUnsigned := +55               // unsigned intent commits to UInteger
myByte : U8 = 200 + 55          // uncommitted 255 realizes as U8
myConcrete := (: U8 = 55) + 1   // concrete U8 operation
```

The aligned model prohibits speculative temporary construction. An uncommitted
integer may fill a concrete-integer input hole only after ordinary source and
receiver rules have already discovered a finite candidate set. It never causes
the compiler to search unrelated types for an operator, constructor, or
conversion.

The closed operation catalog, concrete-operation handoff, and stopping boundary
are aligned below. Remaining open material is explicitly deferred rather than
part of the current resolution model.

### Aligned findings

#### Uncommitted integer evaluation

**Aligned finding:** **Uncommitted integer evaluation** is the
programmer-visible evaluation of numeric source before a concrete integer type
is selected. It uses mathematical integer values rather than the finite range of
an implied machine type.

The term *exact* is not used for this process because Zax already uses it for
exact-width types, exact fit, and exact conversion.

An uncommitted integer carries:

- a mathematical integer value;
- sign intent: unknown, signed, or unsigned; and
- no concrete type, logical width, storage, identity, or runtime
  representation.

The mathematical magnitude is bounded by compiler capacity in practice.
Exceeding that capacity is a compiler resource/capacity failure, not arithmetic
overflow in a hidden concrete integer. The exact cap and resource policy remain
deferred.

An uncommitted integer is never a runtime value. Every complete runtime path
commits it to one concrete type before the value crosses the applicable
expression or control-flow boundary.

#### Sign intent

**Aligned finding:** Bare numeric source begins with unknown sign intent:

```zax
55
-55
```

Mathematical negation changes the value but does not originate sign intent.
Value history does not create intent: an intermediate negative value may later
become positive before any signedness is requested.

Pre-unary `+` is the only operation that originates or toggles sign intent:

| Input intent | Result intent |
| --- | --- |
| Unknown | Unsigned |
| Unsigned | Signed |
| Signed | Unsigned |

```zax
myUnknown := 55
myUnsigned := +55
mySigned := +(+55)
myUnsignedAgain := +(+(+55))
```

Known intent is binding during candidate selection and realization:

```zax
myU8 : U8 = +55       // valid: unsigned intent
myOtherU8 : U8 = +(+55) // error: signed intent cannot realize as U8
myI8 : I8 = +(+55)    // valid: signed intent
```

An operation that must produce one uncommitted integer from several
uncommitted integer operands combines intent as follows:

| Left | Right | Result |
| --- | --- | --- |
| Unknown | Unknown | Unknown |
| Signed | Unknown | Signed |
| Unknown | Signed | Signed |
| Unsigned | Unknown | Unsigned |
| Unknown | Unsigned | Unsigned |
| Signed | Signed | Signed |
| Unsigned | Unsigned | Unsigned |
| Signed | Unsigned | Intent error |
| Unsigned | Signed | Intent error |

```zax
(+5) + 6        // unsigned
(+5) + (+7)     // unsigned
(+5) + (+(+7))  // error: unsigned and signed intent conflict
```

A negative intermediate is permitted while intent remains unknown:

```zax
myValue : U8 = +(999 - 1000 + 2) // final + applies unsigned intent to 1
```

Once unsigned intent exists, an operation producing a negative result is an
intent error:

```zax
myValue := (+5) - 10 // error: unsigned-intent result would be -5
```

#### Size commitment and range

**Aligned finding:** Before a type is selected, intermediate mathematical
values may exceed the eventual destination range:

```zax
myByte : U8 = 1000 - 745 // mathematical 255, then U8 realization
```

A commitment boundary selects a concrete integer identity and logical width.
After commitment, every operation uses that concrete type's range, protected
operation policy, result identity, and costs:

```zax
myConcrete := (: U8 = 250) + 10
// Concrete U8 required addition; its ordinary overflow behavior applies.
```

The operation does not produce an unbounded `260` and reconsider the type.

The minimum signed value requires no impossible positive intermediate:

```zax
myMinimum : I8 = -128
```

Mathematical negation produces uncommitted `-128`, which then realizes directly
as `I8`. There is no temporary positive `I8(128)`.

#### Default commitment

**Aligned finding:** An inferred boundary with no other selected type uses:

- `Integer` for unknown or signed intent; and
- `UInteger` for unsigned intent.

```zax
myInteger := 55
myOtherInteger := +(+55)
myUInteger := +55
```

The preferred default is selected before range checking. If the mathematical
value does not fit, compilation fails and asks the programmer to state a type:

```zax
myValue := 999999999999999999999999999999999999
// error when the value does not fit Integer; state the intended type
```

There is no automatic progression through `Long`, `LongLong`, maximum roles, or
generated exact widths.

An anonymous inferred declaration is an immediate commitment boundary:

```zax
myValue : U8 = (:= 55) + 1
// (:= 55) commits to Integer.
// Integer + Integer produces Integer, which cannot initialize U8 implicitly.
```

The programmer states an earlier type when that is the intent:

```zax
myValue : U8 = (: U8 = 55) + 1
```

#### Candidate holes and selection

**Aligned finding:** An uncommitted integer may fill a declared
concrete-integer input hole after ordinary discovery has produced a finite
candidate set.

Applicable boundaries include:

```zax
myByte : U8 = 55                // typed initialization
destination = 55               // discovered assignment input
consume(55)                    // discovered function parameter
myValue := b + 55              // discovered operator input
myIdentity := MyValue unchecked from 55 // discovered admission input

makeByte : (result : Byte)() = {
  return 55                    // complete result slot
}
```

The shared callable/operator rules still own discovery, mapping, qualification,
visibility, preference, and availability. Uncommitted-integer participation
adds these rules:

1. Sign-incompatible parameter types are not viable for known intent.
2. Concrete identity mismatches do not create implicit conversions.
3. An uncommitted value may realize directly only into a declared
   integer-realization hole.
4. If one candidate survives ordinary selection requirements, select it.
5. If several otherwise viable candidates remain:
   - unknown or signed intent selects a candidate whose input is `Integer`;
   - unsigned intent selects a candidate whose input is `UInteger`;
   - absence of the applicable preferred default leaves an ambiguity.
6. Check the mathematical value against the selected input range only after
   candidate selection.
7. A selected range failure is an error. Selection does not retry a wider or
   otherwise weaker candidate.

```zax
consume(value : Integer)
consume(value : I128)

consume(999999999999999999999999)
// Integer is selected for unknown intent, then range checking fails.
// The compiler does not fall through to I128.
```

Range does not rank overloads:

```zax
consume(value : I8)
consume(value : I16)

consume(200)
// error: ambiguous; the value does not select I16 by failing I8's range
```

Explicit sign intent may filter the set:

```zax
construct(value : U32)
construct(value : I8)

myUnknown := construct(55)      // ambiguous
myUnsigned := construct(+55)    // selects U32
mySigned := construct(+(+55))   // selects I8
myNegative := construct(-55)    // ambiguous: value does not create intent
```

`-55` remains unknown intent. Its negative value does not eliminate `U32`
during candidate selection because range is checked only after selection. If
`U32` were the only surviving candidate, it would be selected and realization
would then fail because `-55` is outside its range.

#### Protected integer operand anchoring

**Aligned finding:** A concrete nonreceiver operand may constrain an already
discovered protected integer-family operation. It does not contribute
receiver-owned declarations.

```zax
myValue := 0 + (: U8 = 5)
```

The symbolic `+` form and protected integer family are known independently of
the right operand. Concrete `U8(5)` constrains the protected same-identity
family to `U8 + U8`; uncommitted `0` then realizes into the left `U8` hole.
For uncommitted-integer participation, this is equivalent to:

```zax
myValue := (: U8 = 0) + (: U8 = 5)
```

The compiler need not create an anonymous declaration or temporary matching
that explanatory rewrite.

The exception is deliberately narrow:

1. the source form must already discover a protected integer-family candidate;
2. the concrete operand must occupy a value position that constrains the same
   integer identity;
3. its type must itself be an exact intrinsic integer or language-defined
   integer role; and
4. every uncommitted peer must pass intent and range checks for that identity.

The same rule applies when nested concrete integer results flow outward:

```zax
myValue := 0 + (55 % (: U8 = 4))
```

The inner protected remainder becomes concrete `U8 % U8`, so its result is
`U8`. That result anchors the outer protected `U8 + U8`.

This rule never borrows custom declarations from the right:

```zax
myValue := 0 foobar (: U8 = 4)
// error: U8 on the right does not provide a receiver-owned `foobar`

myOtherValue := 0 foobar walks slowly uphill (: U8 = 4)
// error unless independently discoverable forms produce one complete tree;
// the compiler does not search U8 or unrelated types for those phrase words
```

Phrase recognition still forms only the structurally complete trees permitted
by independently discoverable language-provided forms and declarations
available from already concrete receivers. A right operand may constrain a
parameter of such a candidate after discovery; it cannot decide phrase extent,
rescan words, or supply a speculative receiver type.

The protected relationship is semantic language design, not a consequence of
representation. An arbitrary identity does not anchor the protected integer
family:

```zax
MyInteger :: identity admit expose type Integer

myValue := 0 + (: MyInteger = 5)
// error: MyInteger is not an integer-family type and cannot contribute its
// exposed receiver-owned `+` declaration from the right
```

The programmer commits both identity operands:

```zax
myValue := (: MyInteger = 0) + (: MyInteger = 5)
```

A transparent alias of `Integer` remains `Integer` and therefore retains the
protected relationship.

#### No speculative temporary construction

**Aligned finding:** An uncommitted integer is not an instance of every integer
type and does not discover type-owned operations through possible temporary
types.

```zax
55 as U16       // error: no concrete receiver for `as`
55 foobar U16   // error: no concrete receiver owner
```

Commitment must be explicit when an operation needs a receiver:

```zax
(: U16 = 55) as SomeType
(: MyInteger = 55) foobar U16
```

A known receiver bounds discovery but does not authorize recursive implied
construction:

```zax
myValue := MyInteger accept 55
// If `accept` requires MyInteger, this is an error.
// The compiler does not implicitly construct a MyInteger to make it viable.

myValue := MyInteger accept (: MyInteger = 55)
// The programmer explicitly supplies the nested construction boundary.
```

If `MyInteger` has constructors from both `U32` and `I8`, this is ambiguous:

```zax
myValue := (: MyInteger = 55)
```

The programmer commits the constructor input:

```zax
myU32Value := (: MyInteger = (: U32 = 55))
myI8Value := (: MyInteger = (: I8 = 55))
```

Committing to an unrelated `U8` would not select either constructor because Zax
does not implicitly widen or change signedness:

```zax
myValue := (: MyInteger = (: U8 = 55))
// error when MyInteger accepts only U32 or I8
```

#### Direct-realization types and identities

**Aligned finding:** Exact intrinsic integers and language-defined integer role
types directly realize uncommitted integers. Transparent aliases preserve that
behavior because they preserve type identity.

```zax
myExact : U8 = 55
myRole : Byte = 55
myTarget : Target.Integer = 55
```

An arbitrary identity does not become an integer merely because it uses an
integer underlying type:

```zax
MyValue :: identity admit expose type Integer
```

`admit` permits declared admission and `expose` adapts eligible underlying
operations. Neither places `MyValue` in the integer family or gives it
fundamental integer realization.

A typed `MyValue` declaration instead performs its finite construction or
admission selection. Uncommitted source may fill concrete integer parameters
declared by those candidates, but it may not recursively construct intermediate
types to make them viable.

Whether a future custom numeric-family mechanism can declare a genuine integer
type is explicitly deferred. Ordinary identity declarations cannot.

Integer-family membership is what makes `U8`, `Byte`, and `Integer` special for
protected operand anchoring. `admit`, `expose`, matching representation, and an
integer underlying type do not grant that membership.

#### Concrete results never reopen

**Aligned finding:** Every function result has the concrete type declared by
the selected function. Compile-time execution does not erase that type or
return a hidden uncommitted integer.

```zax
compileTimeResult : (result : Integer)() = {
  return 55
}

myValue := compileTimeResult() // concrete Integer
myByte : Byte = compileTimeResult()
// error: concrete Integer does not reopen for Byte realization
```

An uncommitted integer may fill a function's complete typed result slot while
the function returns:

```zax
makeByte : (result : Byte)() = {
  return 55 // 55 realizes as Byte at the return boundary
}
```

The caller receives concrete `Byte`.

Resolved prefixed literals likewise have one concrete result type. A prefix
does not leave an uncommitted value for later destination-driven
reinterpretation:

```zax
myValue := b'100001'
// The future literal declaration selected by `b` determines a concrete type.
```

The exact type, prefix catalog, namespace, payload rules, and declaration
syntax remain literal work.

#### Conversion and admission

**Aligned finding:** `as`, optional conversion, narrowing conversion, and other
instance-receiver operations require a concrete source instance:

```zax
55 as U16 // error: uncommitted 55 has no concrete receiver
```

Commit before converting:

```zax
myOptional := (: I32 = 256) as U8?
myNarrow := (: I32 = 256) narrowing as U8
```

Direct realization itself is exact-fit only. It has no hidden optional,
wrapping, saturating, narrowing, or reporting policy.

A type-receiver admission has a known receiver and may expose concrete integer
input holes:

```zax
myValue := MyInteger unchecked from 55
```

It does not authorize speculative source types or recursive temporary
construction.

#### Conditional convergence

**Aligned finding:** A conditional expression must converge to one concrete
result type. Its two final arm results participate in one narrow conditional
convergence hole; uncommitted values never become runtime values.

If both arms remain uncommitted, a permitted outer concrete integer hole
supplies their commitment type:

```zax
myValue : U8 = condition ?? 1 ;; 2
// Each arm realizes as U8 before its runtime path leaves the conditional.
```

Without an outer type, merged sign intent selects the ordinary default:

```zax
myInteger := condition ?? 1 ;; 2
myUInteger := condition ?? +1 ;; +2
```

If one arm is concrete and the other remains uncommitted, the uncommitted arm
adopts the concrete arm's type when that type directly realizes integers:

```zax
myValue := condition ?? (: U8 = 1) ;; 2
// Conditional result is U8.
```

Sign and range are checked after the type is selected:

```zax
mySignError := condition ?? (: U8 = 1) ;; +(+2)
// error: signed intent cannot realize as U8

myRangeError := condition ?? (: U8 = 1) ;; 256
// error: 256 does not fit U8; no fallback to another type
```

An arbitrary identity cannot directly consume the uncommitted arm:

```zax
myValue := condition ?? makeMyValue() ;; 55
// error when the concrete arm is a non-integer MyValue
```

The programmer explicitly commits that arm:

```zax
myValue := condition ?? makeMyValue() ;; (: MyValue = 55)
```

Two concrete arms use ordinary convergence and do not reopen:

```zax
myValue := condition ?? (: U8 = 1) ;; (: Byte = 2)
// error: U8 and Byte are distinct identities
```

The convergence hole applies only to each arm's final result. It never flows
inward to discover a receiver:

```zax
myValue : U8 = condition ?? 55 ^ 77 ;; 1
// error: the first arm has no concrete XOR receiver
```

The programmer commits before the width-dependent operation:

```zax
myValue := condition ?? ((: U8 = 55) ^ 77) ;; 1
```

Nested conditional result positions may share the same convergence type, but
ordinary calls and operators remain independent. Whether a condition is
compiler-known does not change validation or typing; both arms are checked.

#### Compile-time execution

**Aligned finding:** Constant-only inputs suggest or permit compile-time
execution but do not require it by themselves.

Candidate selection, uncommitted integer realization, and execution timing are
separate:

1. Discover and select the callable or operator.
2. Realize uncommitted inputs into the selected concrete parameter types.
3. Execute during compilation only when future compile-time rules require or
   permit it.
4. Otherwise emit an ordinary runtime operation using concrete inputs.

Runtime remains available unless the selected operation or consuming context
requires compile-time execution. Compiler discretion must remain within future
language-defined eligibility and observable-behavior constraints; it cannot
silently move observable effects or substitute compiler-host semantics for
target semantics.

If a typed operation executes during compilation, its ordinary typed policies
still apply. A compile-time-known panic becomes a compiler diagnostic; constant
evaluation does not reopen its operands or result as uncommitted integers.

#### Environment-selected realization

**Aligned finding:** A commitment boundary obtains the selected concrete
identity before checking range:

```zax
myActive : Integer = 42
myTarget : Target.Integer = 42
myCompilerHost : CompilerHost.Integer = 42
myPortable : I32 = 42
```

- `Integer` and `UInteger` use the active execution environment.
- `Target.Integer` and `Target.UInteger` use the target profile.
- `CompilerHost.Integer` and `CompilerHost.UInteger` use the compiler-host
  profile.
- Exact-width identities use their fixed language ranges.

Performing realization in the compiler does not make the active environment
mean compiler host. Exact execution-context selection and profile mechanisms
remain deferred.

### Aligned uncommitted-operation catalog

> **Aligned finding:** This is the closed initial catalog for uncommitted
> integer evaluation. It remains non-authoritative until promoted.

#### Admission test for an uncommitted operation

An operation should participate in uncommitted integer evaluation only when all
of these are true:

1. Its source form is language-provided for uncommitted integers rather than
   discovered through a concrete receiver type.
2. Its meaning does not require a logical width, storage representation,
   mutable place, identity, or implicit temporary construction.
3. It produces either another mathematical integer with one combined sign
   intent or a concrete non-integer result such as `Boolean` that does not
   require the operands to adopt one shared result intent.
4. It does not apply a concrete-width overflow policy or produce an optional or
   reporting structure whose shape depends on a selected integer type.
5. Its failure can be diagnosed during uncommitted evaluation without
   pretending that a concrete integer operation panicked.

Passing this conceptual test does not automatically add an operator. The
following table is the aligned closed catalog.

#### Allowed forms

| Category | Forms | Uncommitted behavior |
| --- | --- | --- |
| Grouping | `(value)` | Preserve mathematical value and sign intent; grouping changes only precedence. |
| Sign intent | Pre-unary `+` | Toggle unknown to unsigned, unsigned to signed, or signed to unsigned. |
| Negation | Pre-unary `-` | Mathematically negate the value and preserve intent; diagnose when an existing unsigned intent would produce a negative result. |
| Arithmetic | `+`, `-`, `*` | Combine operand intent, calculate the mathematical result without concrete-width overflow, and diagnose a negative result under unsigned intent. |
| Division | `/` | Combine intent and use truncation toward zero. A zero divisor is an uncommitted-evaluation error. There is no signed-minimum divided by `-1` case because no finite minimum exists yet. |
| Remainder | `%` | Combine intent; diagnose a zero divisor; use the dividend-sign rule for a nonzero result. |
| Magnitude | `\|value\|` | Return mathematical absolute value and preserve sign intent. No concrete signed-minimum panic exists before commitment. |
| Equality | `==`, `!=` | Compare mathematical values regardless of differing operand intent and return concrete `Boolean`. |
| Ordering | `<`, `<=`, `>`, `>=` | Compare mathematical values regardless of differing operand intent and return concrete `Boolean`. |

Examples:

```zax
mySum : U8 = 200 + 55
myProduct := 9999999999999 * 44444444444
myQuotient := -7 / 3  // proposed mathematical result: -2
myRemainder := -7 % 3 // proposed mathematical result: -1
myMagnitude : U16 = |-999|
myComparison := 1000 < 2000 // concrete Boolean
myCrossIntentComparison := (+5) < (+(+7)) // concrete Boolean
```

The inferred integer examples commit only after their full uncommitted
expression finishes. `myComparison` is already concrete because comparison
returns `Boolean`.

Division or remainder by zero is a compiler diagnostic from uncommitted
evaluation:

```zax
myValue := 1 / 0 // error: zero divisor during uncommitted integer evaluation
```

It is not a runtime panic and does not select a concrete integer merely to use
that type's optional division family.

Magnitude preserves intent because only pre-unary `+` originates or toggles
intent. It changes the mathematical value without erasing a programmer's
explicit signedness request:

```zax
myUnknownMagnitude := |-999|       // unknown intent
myUnsignedMagnitude := |+999|      // unsigned intent
mySignedMagnitude := |-(+(+999))|  // signed intent
```

Comparisons do not merge intent because they do not produce an integer result.
This does not admit custom concrete types into mathematical comparison:

```zax
myResult := (: MyFoo = 0) compare (: MyBar = 0)
// ordinary concrete phrase resolution; not uncommitted integer comparison
```

#### Concrete-only forms and typed handoff

| Category | Forms | Reason |
| --- | --- | --- |
| Policy arithmetic | `+?`, `+%`, `+\|`, `+!`, `+!%`, `+!\|` and corresponding subtraction/multiplication forms | Optional, modular, saturated, and report behavior requires a concrete width and result identity. A concrete same-identity value operand may anchor the protected operation. |
| Policy signedness counterpart | `+?`, `+%`, `+\|`, `+!`, and any reporting variants applied as pre-unary forms | Counterpart range, bit pattern, saturation, optional payload, and report shape require a concrete source type. |
| Policy negation and magnitude | `-%`, `-\|`, `-?`, reporting negation forms, and policy magnitude forms | Their behavior depends on a concrete signed minimum, width, or typed result structure. |
| Optional division/remainder | `/?`, `%?` | Optional result identity and payload require a selected concrete integer type. A concrete same-identity value operand may anchor the protected operation. |
| Increment/decrement | Pre/post `++`, `--`, and policy variants | These mutate a concrete writable place; an uncommitted integer is not a place. |
| Compound mutation | `+=`, `-=`, `*=`, `/=`, `%=` and policy variants | These require an existing typed writable destination. |
| Bitwise value operations | `~`, `&`, `\|`, `^`, `&~`, and word-spelled bitwise forms | Bitwise meaning requires a finite logical width and concrete signed representation. A concrete same-identity value operand may anchor a protected binary form. |
| Counts, masks, reductions, and bit positions | `#...` families and set-bit-position phrases | Results and behavior depend on logical width and associated count types. |
| Shifts and rotations | `<<`, `>>`, `>>>`, `<<%`, `>>%`, and modulo-count phrases | Width controls discarded bits, fill, count behavior, rotation modulus, and result identity. The shifted value must supply the concrete integer type; a count does not determine it. |
| Conversion | `as`, optional `as`, `narrowing as`, and other instance conversions | These require a concrete source receiver. |
| Identity projection | `as Underlying` | Projection requires an existing concrete identity value. |
| User-defined symbolic, phrase, call, index, or mixfix operations | All receiver-owned or visible custom forms | An uncommitted value does not supply a concrete receiver type or authorize global type search. An independently discovered candidate may expose declared concrete-integer holes under the ordinary candidate rules. |

These forms do not cause unanchored operands to default merely so that the
operation becomes available:

```zax
myBitwise := 55 ^ 77 // error: no concrete logical width
myReport := 10 +!% 255 // error: no concrete width for the report operation
myShifted := 1 << 2 // error: no concrete shifted-value width
```

A concrete anchor hands the operation to ordinary typed resolution:

```zax
myBitwise := 55 ^ (: U8 = 77)
myWrapped := 10 +% (: U8 = 255)
myClamped := 100 +| (: U8 = 255)
myReport := 10 +!% (: U8 = 255)
myShifted := (: U8 = 1) << 2
myRotated := (: U8 = 1) <<% 2
```

For a protected same-identity binary operation, the concrete right value may
anchor uncommitted left operands just as a concrete left receiver anchors
uncommitted right operands. A shift count is a distinct operand role and does
not determine the shifted value's identity:

```zax
myCount : BitCount = 2
myShifted := 1 << myCount
// error: BitCount does not determine the left integer type
```

#### Stopping boundary

**Aligned finding:** Uncommitted integer evaluation stops permanently for an
expression node as soon as that node has a concrete type. General expression
resolution continues outward.

```zax
myValue := 0 + (: U8 = 5)
```

For uncommitted participation, this becomes the equivalent fully committed
operation:

```zax
myValue := (: U8 = 0) + (: U8 = 5)
```

Uncommitted integer evaluation stops for that `+` node. Ordinary typed
resolution completes `U8 + U8 -> U8`; broader typed constant evaluation may
calculate the value during compilation when permitted.

The stopping rule is local. An enclosing node may contain another uncommitted
operand:

```zax
myValue := (0 + (: U8 = 5)) + 1
```

The inner result is concrete `U8`. It anchors the outer protected `U8 + U8`, so
the separate uncommitted `1` realizes as `U8`. The inner result itself never
reopens.

Optional and reporting forms return concrete typed structures and never enter
the uncommitted result domain:

```zax
myValue := 10 + ((0 +!% (: U8 = 255)).value)
```

The inner protected reporting addition is anchored as `U8`, and uncommitted `0`
realizes into its `U8` input hole. The selected operation returns a concrete
report; ordinary field resolution gives `.value` concrete type `U8`. That
concrete field anchors the outer protected `U8 + U8`, where uncommitted `10`
realizes as `U8`.

Optional and reporting forms never re-enter uncommitted integer resolution.
They produce their ordinary typed optional/reporting results. If their complete
typed operation is compiler-known, broader constant evaluation may execute it,
but all aligned no-lookahead, identity, and no-speculative-temporary rules still
apply.

No other current protected integer operation belongs in the initial
uncommitted catalog. `delta` and `distance` select associated result types from
a concrete operand identity, and three-way comparison still lacks its concrete
ordering result type. A future pure mathematical integer operation must satisfy
the same admission test before joining the closed catalog.

### Deferred and superseded material

#### Literal syntax and literal operators

**Aligned deferral:** Attached literal payloads use single quotes, never
backticks:

```zax
b'100001'
b8'10011011'
```

A resolved prefixed literal has a concrete type. Prefix recognition therefore
does not use expression-result lookahead and does not leave an uncommitted
integer for a later destination to reinterpret.

The following remain raw literal work:

- the core and custom prefix catalog;
- whether `b`, `b8`, or another prefix denotes radix, concrete numeric type,
  bit pattern, or another result family;
- prefix namespace, ownership, visibility, ambiguity, and imports;
- literal declaration syntax and compile-time execution requirements;
- payload digit alphabets, case, separators, grouping, emptiness, escaping, and
  multiline behavior;
- interaction with strings, characters, byte sequences, and merged literals;
- signed minimum behavior for typed prefixed literals; and
- a literal-only typed suffix candidate.

The literal-only suffix candidate is deliberately narrower than general type
ascription:

```zax
myA := 55 : U8
myB := (55 : U8)

myC := (55 + 88 : U8) // intended error: not a bare literal
myD := (+55 : U8)     // intended error: not a bare literal
```

It would be sugar for `(: U8 = 55)` only when the source operand is one bare
numeric literal. Its grammar, precedence, whitespace, and interaction with
unary parsing require future literal/source review. It is not accepted syntax
by this work.

The lasting raw destination is `project/raw/literal-operators.md`. That file has
not been edited by the current authorization.

#### Compile-time execution and compiler capacity

Compile-time scheduling, required versus inferred execution, allocation,
termination, side effects, environment choice, and resource policy remain in
`project/raw/compile-time-execution.md`.

The compiler-supported maximum magnitude for uncommitted evaluation exists but
is not selected here. It is separate from:

- the range of `Integer` or another concrete type;
- the maximum realizable integer width in a language version; and
- runtime unbounded-integer design.

Compiler capacity exhaustion must not silently choose a concrete width or mimic
ordinary integer overflow.

#### Custom numeric families

Ordinary identities are not integers. A future custom numeric-family mechanism
may decide whether programmer-defined types can participate as genuine integer
types or declare direct literal realization. That mechanism remains in
`project/raw/numeric-type-families.md`.

No such future capability authorizes recursive implied construction or global
operator discovery in the current model.

#### CPU profiles and execution environments

CPU-provider format, profile versioning, active execution-context selection,
and compiler-host/target coexistence remain in
`project/raw/cpu-provider-model.md` and
`project/raw/compile-time-execution.md`.

Current integer realization is constrained only to use the concrete identity
and environment selected before fit checking and never to infer compiler-host
semantics merely because evaluation occurs in the compiler.

#### Superseded uncommitted function-result candidate

The initial reconstruction contemplated an ordinary compile-time function
returning an uncommitted integer. That candidate is superseded.

Every function result has the concrete type declared by its selected
prototype. Existing current/raw wording that contemplates
`compileTimeResult()` returning an uncommitted integer is legacy/candidate
material requiring disposition during eventual promotion; it is not part of
the aligned model.

### Candidate diagnostics

Diagnostics should distinguish:

1. malformed numeric source or prefixed payload;
2. unknown or ambiguous future literal prefix;
3. conflicting known sign intent;
4. missing concrete receiver for an instance operation;
5. ambiguous finite candidate holes;
6. no applicable preferred `Integer`/`UInteger` candidate among several
   survivors;
7. range failure after candidate selection;
8. incompatible concrete identity;
9. use of a width-dependent operation before commitment;
10. uncommitted-evaluation domain failure such as division by zero;
11. compiler magnitude/resource exhaustion;
12. unavailable profile or execution environment; and
13. failure of a selected typed operation during broader constant evaluation.

A realization failure should name the mathematical value, known sign intent,
selected public type, range, and deciding boundary:

```text
cannot realize integer value 256 as U8
U8 represents 0..255; U8 was selected by the declaration of myByte
```

When candidate preference selected `Integer` or `UInteger`, the diagnostic
should say so and should not suggest that another overload would have been
tried after range failure.

### Candidate lasting ownership

This remains an ownership hypothesis for the later documentation-fit dry run:

- A cohesive integer-literal owner should teach uncommitted integer evaluation,
  sign intent, commitment, realization, defaults, candidate holes, conditional
  convergence, failures, and the final accepted operation catalog.
- `language/integers.md` should retain concrete integer identities, ranges,
  profiles, and a concise direct-realization handoff.
- `language/integer-operator-catalog.md` should own concrete protected integer
  operations and identify which plain forms also have an uncommitted
  mathematical meaning.
- `language/operator-catalog.md` should retain forms, fixity, and precedence.
- `language/function-invocation.md` should retain shared candidate selection,
  narrow expected-result context, and conditional branch convergence.
- `language/source-structure.md` should retain quote attachment and eventual
  lexical digit/separator rules.
- Future literal documentation should own concrete prefixed literal
  declarations and payload behavior.
- Future compile-time documentation should own execution eligibility and
  context, not an uncommitted function-result type.

No promotion, raw-input update, owner-document correction, structural change,
staging, or version-control mutation is authorized or performed by this update.

## Dispositions and promotion dry run

### Result

**PASS — 2026-08-31**

The aligned findings have a cohesive human-developer-facing owner, every
cross-feature consequence has one current owner or indexed raw destination, and
the promotion can remove current contradictions without deciding deferred
literal-prefix, compiler-capacity, generic, reflection, or CPU-profile
mechanisms.

The dry run found one material integration pressure that does not invalidate
the design: current endianness teaching and several legacy pages use `h'...'`
as though that unresolved prefix already has a compatible result type.
Promotion can replace those incidental uses with explicitly typed decimal
backing values while preserving the useful prefix and merged-literal evidence
in raw literal input. The exact prefix catalog and result types can therefore
remain deferred without leaving current documentation dependent on them.

This PASS does not authorize promotion.

### Proposed structure and reading path

Create one current concept owner:

```text
language/integer-literals.md
```

No new directory or language-level router is needed. The public reading path
will be:

```text
README.md
  -> index.md
    -> language/integer-literals.md
      -> language/integers.md
      -> language/integer-operator-catalog.md
      -> language/operators.md
      -> language/function-invocation.md
```

The new owner is justified by cohesive teaching pressure rather than length.
Without it, a programmer would have to reconstruct one feature across integer
types, concrete operators, callable selection, declarations, conditional flow,
source structure, and compile-time raw input.

`language/integer-literals.md` will own:

- uncommitted integer evaluation and its compiler-only boundary;
- unknown, signed, and unsigned intent;
- pre-unary `+` intent toggling;
- mathematical negation and minimum signed realization;
- default commitment to `Integer` or `UInteger`;
- exact-fit realization and post-selection range failure;
- the closed uncommitted-operation catalog;
- finite candidate holes and default candidate preference;
- protected integer operand anchoring, including the narrow concrete-RHS rule;
- the stopping boundary and handoff to ordinary typed resolution;
- no speculative temporary construction;
- direct-realization eligibility and arbitrary identity exclusion;
- concrete function and prefixed-literal result boundaries;
- conditional convergence of final uncommitted arms;
- interaction with concrete conversion/admission;
- environment-selected destinations at programmer-visible depth;
- costs, diagnostics, source stability, and deferred boundaries.

The owner will teach in this order:

1. ordinary inferred and typed examples;
2. the uncommitted value and commitment mental model;
3. sign intent and default selection;
4. mathematical operations and minimum signed values;
5. candidate holes and protected operand anchoring;
6. concrete-operation stopping and conditional convergence;
7. identities, calls, conversions, and environment-selected types;
8. failures, costs, diagnostics, stability, and deferred literal syntax.

That order passes the cold-reader and vocabulary-before-use checks. Exhaustive
operator and candidate tables follow the ordinary model rather than opening the
document.

`index.md` will add the new owner to both the task-oriented start route and the
current conceptual-design list. Root `README.md` remains the repository router
and needs no change.

Raw inputs remain outside ordinary reading paths. Current owners will not cite
this work item, archived work, or raw files.

### Finding-to-owner map

| Aligned finding | Lasting owner | Required integration |
| --- | --- | --- |
| Uncommitted integer state and evaluation boundary | `language/integer-literals.md` | Add concise cross-cutting terms to `language/terms.md`. |
| Unknown/signed/unsigned intent and pre-unary `+` toggling | `language/integer-literals.md` | Distinguish uncommitted intent from concrete counterpart behavior in both operator catalogs. |
| Mathematical negation, negative intermediates, and minimum signed realization | `language/integer-literals.md` | Keep concrete negation policies in `language/integer-operator-catalog.md`. |
| Default `Integer`/`UInteger` commitment and oversized-default failure | `language/integer-literals.md` | Link inferred declaration behavior from `language/declarations-and-bindings.md`; retain concrete ranges in `language/integers.md`. |
| Range checked after candidate selection with no fallback | `language/integer-literals.md` | Add the domain-specific preference boundary to `language/function-invocation.md` and operator selection summary. |
| Closed mathematical operation catalog | `language/integer-literals.md` | Keep form spelling and precedence in `language/operator-catalog.md`; keep concrete policies in `language/integer-operator-catalog.md`. |
| Concrete-only operation anchor and stopping boundary | `language/integer-literals.md` | Add the protected operand-constraint exception to `language/operators.md` without weakening receiver-owned discovery. |
| Concrete RHS anchors protected same-identity integer operations | `language/integer-literals.md` and shared discovery rule in `language/operators.md` | Clarify that RHS constrains an already discovered protected candidate and never contributes declarations. |
| Direct realization into exact and language-role integers | `language/integer-literals.md` | Reduce `language/integers.md` to a concise type/range-side handoff. |
| Arbitrary identities are not integers | `language/identity-types.md` | Link the integer-specific realization boundary; retain future genuine custom integer pressure in raw numeric input. |
| Constructor, function, assignment, return, and operator input holes | `language/integer-literals.md` | Add local handoffs in declarations, construction, invocation, and operators without duplicating the selection algorithm. |
| No speculative temporary construction or global type search | `language/integer-literals.md` | Preserve general discovery and phrase ownership unchanged; add only the explicit integer exception in `language/operators.md`. |
| Function results are always concrete | `language/function-invocation.md` | Remove the competing uncommitted-function-result candidate from integers and raw compile-time input. |
| Resolved prefixed literals are concrete | Future literal owner; current boundary in `language/integer-literals.md` | Preserve exact attached-quote tokenization in source structure and all unresolved prefix mechanics in raw literal input. |
| Conditional convergence of final uncommitted arms | `language/core-flow-control.md` | Keep callable branch-specific selection in `language/function-invocation.md`; integer owner teaches the literal-specific application. |
| `as` and policy conversions require a concrete receiver | `language/integer-operator-catalog.md` | Integer-literal owner explains commitment before conversion; no new conversion form. |
| Constant-only inputs permit but do not require compile-time execution | Future compile-time owner | Correct raw compile-time input and retain only the programmer-visible typed handoff in the integer-literal owner. |
| Active, target, compiler-host, and exact destination realization | `language/integers.md` | Integer-literal owner applies the already selected identity; CPU-provider and execution-context mechanisms remain raw. |
| Compiler-capacity failure is not concrete integer overflow | Future compile-time/numeric owners | Preserve the existence and distinction of the cap without selecting a number or implementation mechanism. |
| Literal-only `55 : U8` sugar and prefix/payload mechanics | `project/raw/literal-operators.md` | Keep representative valid/intended-error source and activation pressure; do not present the syntax as accepted. |

### Deferred destinations and constraints

| Deferred concern | Live destination | Activation pressure | Constraint on current promotion |
| --- | --- | --- | --- |
| Prefix catalog, namespace, declaration syntax, payload digits, separators, grouping, escaping, multiline behavior, typed versus bit-pattern meaning, and literal-only type suffix | `project/raw/literal-operators.md` | Literal parsing, custom literals, typed prefixes, or concatenation work | Attached payloads continue to use single quotes; a resolved prefix has one concrete type; current teaching cannot rely on a particular `b`, `b8`, `o`, `d`, or `h` result. |
| Compile-time scheduling, eligibility, side effects, resource policy, and active host/target context | `project/raw/compile-time-execution.md` | Compile-time execution work | Every selected function result remains concrete; constant inputs do not mandate execution during compilation. |
| Compiler-supported uncommitted magnitude cap and runtime unbounded integers | `project/raw/compile-time-execution.md` and `project/raw/numeric-type-families.md` | Resource-policy, arbitrary-width, or unbounded-numeric work | Capacity exhaustion is neither hidden concrete overflow nor permission to choose a different runtime type. |
| Genuine custom integer families and direct-realization participation | `project/raw/numeric-type-families.md` | Custom/generic numeric-family work | Ordinary identities, including `admit expose` identities, remain non-integer types. |
| Generic/computed destination-type syntax | `project/raw/type-parameters-and-generics.md` | Generic or computed-type work | A concrete type identity must be available before realization; this work invents no generic syntax. |
| CPU-provider format, profile versions, and compiler-host/target coexistence | `project/raw/cpu-provider-model.md` | Target-profile work | The selected public integer identity and environment determine range; compiler execution does not imply compiler-host identity. |
| Literal spelling/provenance reflection | `project/raw/reflection.md` | Source reflection or stable tooling contract | Spelling is not part of integer type identity; ordinary diagnostics may still quote source without a reflective contract. |
| Floating-point and other numeric intent | `project/raw/numeric-type-families.md` | Floating/fixed/decimal/unbounded numeric work | Current sign intent is integer-specific and does not claim a complete numeric-intent lattice. |

The CPU-provider, generic, and reflection raw files already preserve the needed
pressure and require no promotion edit. Literal, compile-time, and numeric raw
files require focused updates because this work consumes or supersedes parts of
their current input.

### Exact proposed promotion change set

The cohesive promotion set is exactly these 24 files.

#### New current owner

1. **Create `language/integer-literals.md`.** Write the human-facing concept
   owner in the teaching order above. Use only unprefixed numeric source for
   accepted literal behavior; mention prefixed literals only to establish their
   concrete-result and attached-quote boundary.

#### Public routing and terminology

2. **Update `index.md`.** Add the integer-literal owner to the start route and
   current conceptual-design list; keep raw/project material out of the public
   route.
3. **Update `language/terms.md`.** Add concise entries for *commitment
   boundary*, *integer realization*, *uncommitted integer*, and *uncommitted
   integer evaluation*, each linking to the new owner.

#### Current concept owners

4. **Update `language/integers.md`.** Replace the uncommitted
   `compileTimeResult()` candidate with the aligned rule that functions return
   concrete declared types; retain default initialization and concrete
   type/range/profile ownership; link direct realization to the new owner and
   update metadata boundaries.
5. **Update `language/integer-operator-catalog.md`.** State that this catalog
   owns concrete protected operations; link plain mathematical
   uncommitted forms to the new owner; distinguish pre-unary intent toggling
   from concrete signedness counterpart behavior; preserve all concrete policy
   tables.
6. **Update `language/operator-catalog.md`.** Keep exact forms and precedence;
   clarify that the integer-literal owner determines which plain forms
   participate before commitment and that policy/width-dependent forms require
   typed resolution.
7. **Update `language/operators.md`.** Add the protected integer operand
   anchoring exception after general discovery: a concrete same-identity
   integer operand may constrain an already discovered protected family,
   including from the RHS, but never contributes receiver-owned declarations,
   phrase extent, or speculative type search.
8. **Update `language/source-structure.md`.** Preserve attached single-quote
   versus separated phrase-fence behavior; replace “complete literal
   realization remains future” with a handoff that separates current
   unprefixed integer realization from deferred prefixed-literal mechanics.
9. **Update `language/declarations-and-bindings.md`.** Link inferred and typed
   integer initialization to the new owner and clarify that assignment may
   expose a finite concrete integer input hole without becoming backward
   expected-result inference.
10. **Update `language/function-invocation.md`.** Add the domain-specific
    uncommitted-integer input preference and no-range-fallback handoff; state
    that every selected function result is concrete; integrate final-arm
    conditional holes without allowing outer context to invent inner
    receivers.
11. **Update `language/core-flow-control.md`.** Teach the narrow conditional
    convergence rule for two uncommitted final arms, one concrete integer arm,
    and already concrete incompatible arms; preserve validation of both paths
    and prohibit uncommitted runtime values.
12. **Update `language/construction-and-destruction.md`.** Clarify that a
    discovered constructor's concrete integer parameter may consume an
    uncommitted input, while viability never recursively constructs a temporary
    merely to satisfy another constructor.
13. **Update `language/identity-types.md`.** State that integer underlying
    storage, `admit`, and `expose` do not grant integer-family membership or
    direct integer realization; transparent aliases remain the same type.
14. **Update `language/endianness.md`.** Replace semantic uses of unresolved
    `h'...'` with explicitly typed decimal `U32` backing values and hexadecimal
    explanatory comments, preserving the endian examples without assuming a
    prefix result type.

#### Raw future input

15. **Update `project/raw/literal-operators.md`.** Remove consumed unprefixed
    sign/default and superseded uncommitted-function-result material; preserve
    attached single-quote syntax, concrete prefix-result requirement, prefix
    ownership/namespacing, payload behavior, typed-versus-bit-pattern pressure,
    literal-only `55 : U8` sugar, string/custom/merged literal evidence, and
    activation/retirement criteria.
16. **Update `project/raw/compile-time-execution.md`.** Replace the
    uncommitted-function-result candidate with concrete declared function
    results; preserve constant-input eligibility, required versus inferred
    execution, environment context, failure, and resource questions.
17. **Update `project/raw/numeric-type-families.md`.** Add the future question
    of declaring a genuine custom integer family/direct-realization contract;
    state that ordinary identities do not receive it.
18. **Update `project/raw/README.md`.** Narrow the literal row from general
    unprefixed integer realization to unresolved prefixed/custom literal
    behavior and include the custom-integer realization pressure in the numeric
    row.

#### Legacy pages affected by unresolved prefix type

19. **Update `basics.md`.** Consume the legacy intrinsic-literal section:
    route current unprefixed integer behavior to the new owner, move the
    smallest useful prefix/string/custom/merged examples into raw literal
    input, and remove duplicate or superseded teaching from the public legacy
    page.
20. **Update `casting.md`.** Replace the two incidental `h'16f'` merged-literal
    fragments with equivalent direct non-ASCII wide-string content while
    preserving their string-conversion teaching; preserve unique merged-literal
    evidence in raw literal input.
21. **Update `pointers.md`.** Replace the incidental typed
    `U32 = h'ABCDEF12'` example with the corresponding decimal value and a
    hexadecimal comment.
22. **Update `meta-functions.md`.** Replace the incidental typed
    `U64 = h'ABCDEF'` example with the corresponding decimal value and a
    hexadecimal comment.
23. **Update `type-definition.md`.** Replace the incidental assignment of
    `h'ABCDEF01'` to `U32` with the corresponding decimal value and a
    hexadecimal comment.

#### Promotion record

24. **Update `project/work/011-integer-literals.md`.** Record the completed
    finding dispositions, applied owner map, promotion validation result, and
    any deviation from this exact set. Do not archive or create work item `012`
    as part of promotion.

No other file is in the proposed promotion set:

- `README.md` already routes through `index.md`;
- `project/README.md` remains pointed at active work `011` until closure;
- `language/operator-phrases.md` already owns receiver-only phrase discovery
  consistently and needs no duplicate integer rule;
- `language/mixfix-operators.md` needs no change because the new rule does not
  alter candidate-tree formation;
- `project/raw/cpu-provider-model.md`,
  `project/raw/type-parameters-and-generics.md`, and
  `project/raw/reflection.md` already hold the applicable deferrals;
- historical `operator.md` already routes literal evidence to the raw literal
  input and remains unchanged; and
- no archive file participates in promotion.

### Legacy and raw disposition

Promotion consumes legacy material by value:

- ordinary decimal/default/sign/realization teaching moves to the new current
  owner;
- concrete integer type/range and operation material remains in its current
  owners;
- prefix, custom literal, string payload, merged literal, and typed-suffix
  evidence moves to the focused raw literal input;
- incidental prefix uses in unrelated examples become typed decimal source;
- the superseded uncommitted function-result candidate is removed from current
  and raw teaching rather than preserved as a live alternative.

`project/raw/literal-operators.md`,
`project/raw/compile-time-execution.md`, and
`project/raw/numeric-type-families.md` remain live after promotion because each
retains independently useful unresolved input and activation pressure. None is
ready to retire.

No path moves during promotion. After promotion and validation, closure would
separately:

1. mark this work item historical;
2. move it unchanged in name to
   `project/archive/work/011-integer-literals.md`;
3. align and create work item `012`; and
4. update `project/README.md`.

Those closure actions require separate discussion and authorization and are not
part of this promotion set.

### Promotion validation plan

The authorized promotion must pass all of these checks:

1. Every current owner states conceptual, non-specification, and
   not-implemented status without claiming compiler conformance.
2. The new owner passes cold-reader, vocabulary-before-use,
   concrete-paraphrase, teaching-versus-reference, and two-pass promotion
   checks.
3. Every accepted error example carries an inline `// error` marker.
4. No current owner cites this work item, archived work, or raw input.
5. `index.md` reaches the new owner through both task and catalog routes.
6. Local links and anchors resolve in repository and website Markdown.
7. `language/integers.md` and raw compile-time input no longer claim that an
   ordinary function may return an uncommitted integer.
8. Protected RHS anchoring is stated as operand constraint after discovery,
   never right-operand declaration contribution.
9. Arbitrary identities never gain integer-family realization from storage,
   `admit`, or `expose`.
10. Conditional convergence never leaves an uncommitted runtime value or sends
    a type inward to discover an operator.
11. Range failure occurs after candidate selection and never triggers fallback.
12. A repository search finds no backtick literal examples.
13. Current semantic examples do not depend on an unresolved `h'...'` result
    type; the source-structure tokenization example may remain because it does
    not claim result semantics.
14. Deferred literal examples in raw input use attached single quotes and mark
    unaccepted syntax or behavior clearly.
15. Legacy pages retain no duplicate current integer-literal teaching and no
    incidental unmarked prefix dependency.
16. Raw indexes accurately describe what remains unresolved.
17. Markdown whitespace checks pass.
18. The final promotion diff contains exactly the 24 files above.
19. The staged and unstaged review boundary chosen by the language maintainer
    remains intact until separately authorized.

### Dry-run conclusion

The owner structure is sufficient, the teaching path is cohesive, every
aligned finding and deferral has a live destination, and the exact promotion
set can remove all discovered contradictions. The pre-promotion documentation
fit dry run therefore **PASSES**.

Wait for separate promotion authorization.

### Promotion result

**PROMOTED AND VALIDATED — 2026-08-31**

The language maintainer separately authorized the exact dry-run promotion set.
Promotion applied all 24 planned files with no added, omitted, moved, or renamed
path:

- `language/integer-literals.md` now owns the cohesive current
  human-developer-facing concept;
- public routing and terminology reach that owner;
- integer, operator, declaration, invocation, flow, construction, identity,
  source, and endian owners contain their local integration boundaries without
  duplicating the complete model;
- raw literal, compile-time, and numeric inputs retain only unresolved future
  pressure and remain indexed;
- legacy literal teaching was consumed by value, and incidental unresolved
  prefix uses were removed from unrelated examples; and
- this work item records the promotion while remaining active and
  non-authoritative until separately authorized closure.

Promotion validation passed:

1. all changed Markdown links and heading anchors resolve;
2. all changed Markdown code fences balance;
3. staged and unstaged whitespace checks pass;
4. current language owners contain no links into `project/raw/`;
5. no backtick-prefixed literal example remains;
6. the only current `h'...'` example is the source-structure attachment example,
   which claims tokenization rather than a result type;
7. ordinary function results are concrete in current and raw teaching;
8. protected RHS anchoring is documented as post-discovery constraint rather
   than right-operand declaration contribution;
9. arbitrary identities receive no integer-family behavior from storage,
   `admit`, or `expose`;
10. error examples identify their failure in source comments;
11. the public index reaches the new current owner;
12. the promotion changed exactly the 24 files named by the dry run; and
13. no archive, current-work pointer, branch, staged baseline, or version-control
    history was changed.

No promotion deviation requires a revised dry-run result. Archival, work item
`012`, staging the promotion layer, committing, and pushing remain separate
actions requiring their own authorization.

### Post-promotion teachability correction

**APPLIED AND VALIDATED — 2026-08-31**

Maintainer review found that several cross-owner integration passages were
semantically accurate but opened with compressed project terminology before a
direct-entry reader had a concrete model. One advanced constructor interaction
also appeared too early in the construction document.

The separately aligned and authorized correction:

- moved integer constructor-overload behavior after constructor argument
  mapping and gave it a dedicated subsection;
- rewrote affected declaration, invocation, integer, operator, flow, raw
  compile-time, raw literal, and raw numeric handoffs to lead with source and
  observable behavior;
- changed technical section openings in the integer-literal owner to introduce
  examples before formal selection and stopping terminology;
- made the public index describe the reader task in ordinary language; and
- strengthened `project/documentation.md` so direct-entry, example-first,
  cross-owner handoff, prerequisite placement, and terminology-density checks
  apply to every edited owner and independently linked section.

The correction changed exactly these 15 files:

1. `index.md`;
2. `language/construction-and-destruction.md`;
3. `language/core-flow-control.md`;
4. `language/declarations-and-bindings.md`;
5. `language/function-invocation.md`;
6. `language/integer-literals.md`;
7. `language/integer-operator-catalog.md`;
8. `language/integers.md`;
9. `language/operator-catalog.md`;
10. `language/operators.md`;
11. `project/documentation.md`;
12. `project/raw/compile-time-execution.md`;
13. `project/raw/literal-operators.md`;
14. `project/raw/numeric-type-families.md`; and
15. `project/work/011-integer-literals.md`.

No language behavior, owner boundary, deferral, or promotion disposition
changed. The original 24-file promotion record remains an accurate historical
boundary; this is a later feedback correction layered over it.

Validation confirmed that:

- the staged promotion baseline remains unchanged;
- the 15-file feedback set contains no extra or missing path;
- all changed Markdown links and heading anchors resolve;
- all changed code fences balance;
- staged and unstaged whitespace checks pass;
- the identified abstract opening phrases no longer remain; and
- the current integer-literal owner and each edited local handoff retain one
  consistent language model.

### Continued review after promotion

> **Maturity of this section:** The findings explicitly marked **Aligned
> finding** reflect subsequent maintainer agreement but have not been promoted.
> Illustrative syntax remains a starting proposal rather than accepted grammar.

Post-promotion review exposed material operator, contextual-construction,
identity, optional, partial, endian, and documentation consequences. The
earlier promotion and validation records remain accurate historical boundaries,
but the current wider documentation no longer contains the complete aligned
model.

**Current documentation-fit status: FAIL.** A revised pre-promotion dry run and
separate promotion authorization are required after these findings are captured
and reviewed. This status does not invalidate the earlier dry run at the time it
was performed; it records that later design review changed the required owner
set.

#### Width-invariance decides uncommitted operations

**Aligned finding:** An operator may act on uncommitted integers when every
sufficiently wide concrete interpretation that can contain its operands and
mathematical result produces the same decoded mathematical result.

The compiler does not literally evaluate several widths. This is the
programmer-visible test for whether finite width contributes meaning.

For each operand, mathematical value and sign intent determine the fitting
interpretation families:

| Value and intent | Fitting families considered by the operation test |
| --- | --- |
| Nonnegative, unknown intent | Signed and unsigned |
| Negative, unknown intent | Signed only |
| Any value, signed intent | Signed only |
| Nonnegative, unsigned intent | Unsigned only |
| Negative, unsigned intent | None; intent error |

A negative value does not create signed intent or rank a later overload. It
only means an unsigned representation cannot contain that operand while the
uncommitted operation's mathematical meaning is tested.

This operation-meaning test is separate from post-selection range checking:

```zax
myUnsigned : U8 = 240 ^ 15 // U8(255)
mySigned : I8 = 240 ^ 15 // error: mathematical 255 does not fit I8
```

The intermediate `240` need not fit `I8`; only the final mathematical result is
realized against the destination.

#### Expanded uncommitted shift and bitwise catalog

**Aligned finding:** Mathematical left and right shift and the width-invariant
binary bitwise operations join uncommitted integer evaluation.

Left shift uses:

```text
value << count = value * 2^count
```

The count must be an uncommitted nonnegative mathematical integer. The operation
may grow the value up to the compiler-capacity boundary:

```zax
myFour := 1 << 2
myLarge := 1 << 55
myNested := 1 << (1 << 3) // 256
```

A concrete count does not determine the shifted value's type:

```zax
myValue := 1 << (: U8 = 3)
// error: U8 is a count value here, not a type for the left operand
```

Once the shifted value is concrete, ordinary finite-width shift behavior takes
over:

```zax
myByte := (: U8 = 1) << 2
```

Mathematical `>>` uses infinite sign extension, equivalently floor division by
`2^count`, and is width-invariant for every fitting signed or unsigned operand.
Zero-fill `>>>` and rotations remain concrete-width operations.

Binary:

```zax
left & right
left | right
left ^ right
left &~ right
```

use infinite sign extension for uncommitted operands. They produce the same
decoded mathematical result at every sufficiently wide fitting width, including
when an operand is negative:

```zax
myValue := -1 ^ 55 // mathematical -56
```

These operations combine sign intent because they produce one integer result.
Conflicting signed and unsigned intent remains an intent error.

Complemented-result forms such as NAND, NOR, XNOR, and OR-NOT remain
concrete-width operations unless separately shown to pass the same
width-invariance test for their complete input domain. Counts, masks,
reductions, and bit positions remain concrete because their result types and
zero/negative behavior require separate review.

#### Complement depends on value and intent

**Aligned finding:** Uncommitted complement is available only when the
width-invariance test produces one mathematical result for the operand's value
and known intent.

Unknown positive values are rejected:

```zax
myValue := ~1 // error: signed and unsigned results differ
```

Unsigned intent is rejected because the result changes with unsigned width:

```zax
myValue := ~(+1) // error: no width-independent unsigned result
```

Signed intent permits complement for positive or negative values:

```zax
myNegative := ~(+(+1)) // signed mathematical -2
myZero := ~(-(+(+1)))  // signed mathematical 0
```

An unknown negative value also permits complement because only signed
representations can contain the operand:

```zax
myZero := ~(-1) // unknown-intent mathematical 0, then Integer
```

Every inner expression must independently succeed:

```zax
myError := (~1) ^ 55 // error at ~1
myValue := (~(-1)) ^ 55 // 55
```

An outer bitwise operation cannot repair a width-dependent inner complement.

#### Failure categories

**Aligned finding:** Diagnostics distinguish:

1. **No invariant mathematical result:** fitting interpretations produce
   different values, as for unknown positive `~1`.
2. **Intent error:** known intent is internally invalid or conflicting, as for
   `+(-1)` or `(+5) ^ (+(+7))`.
3. **Range failure:** one valid mathematical result exists but does not fit the
   concrete type selected later.

Value and intent jointly decide the first two checks. Neither changes the rule
that value magnitude does not rank overloads.

#### Authorized partials may relatch selection

**Aligned finding:** Once a future authorized and visible partial declaration
is part of a type, its constructors and operators participate exactly as though
they were written in the original definition. Candidate preference does not
depend on partial provenance.

```zax
Foo :: type {
  +++ final : ()(rhs : U8) = {
  }
}

myFoo : Foo = 100 // selects U8

// Illustrative future partial syntax.
FooExtension :: partial Foo {
  +++ final : ()(rhs : Integer) = {
  }
}

myOtherFoo : Foo = 100 // selects Integer when the partial is present
```

Relatching is an accepted source-compatibility consequence of deliberately
changing the type's surface. Future partial work must decide who may contribute
that surface and how it becomes stable and reproducible; it must not make
selection depend on declaration or import order or ignore an admitted candidate
merely because it came from a partial.

This pressure belongs in `project/raw/partial-types.md`.

#### Contextual completion mode

**Aligned finding:** `contextual` and `explicit` are one **completion mode**
applied to constructors, admissions, operators, and any future callable
categories that choose to participate.

- `contextual` permits a specifically allowed source context to complete one
  missing typed value through one bounded construction step.
- `explicit` requires source to provide the complete concrete value before the
  declaration is selected.
- Omission defaults to `explicit`.
- Writing `explicit` redundantly may document deliberate opt-out.

The terms are deliberately not `implicit`/`explicit`: `contextual` does not
authorize general implicit conversion.

Illustrative starting syntax:

```zax
+++ contextual final : ()(rhs : Integer) = {
}

operator binary '+' contextual final : (
  result : MyInteger
)(
  rhs : MyInteger
) = {
}
```

Explicit-only forms:

```zax
+++ explicit final : ()(rhs : Integer) = {
}

operator binary '+' explicit final : (
  result : MyInteger
)(
  rhs : MyInteger
) = {
}
```

Exact keyword placement remains subject to source/declaration integration, but
the common vocabulary, semantics, and explicit omission default are aligned.

#### Direct-before contextual operator fallback

**Aligned finding:** Peer-directed construction is a bounded fallback rather
than a competitor to ordinary direct candidates.

For a contextual operator:

1. form exactly one operator tree and exact component without the speculative
   receiver type;
2. complete ordinary direct discovery, viability, and selection first;
3. if a direct candidate wins, use it;
4. if direct candidates are ambiguous, report ambiguity;
5. if a selected direct candidate later fails range, report that failure;
6. if a uniquely best direct candidate is unavailable, report unavailability;
7. attempt contextual fallback only when no direct candidate is viable;
8. obtain one receiver type `T` from one already concrete peer/type anchor;
9. require exactly one applicable contextual construction into `T`;
10. discover only the already fixed operator form on `T`;
11. require the `T`-owned operator to be contextual and to accept the existing
    peer;
12. insert at most that one construction, preserving source evaluation order and
    every constructor effect; and
13. never chain construction, conversion, admission, phrase segmentation, or
    another fallback.

Both the constructor and operator must opt into contextual completion:

```zax
myValue := 0 + (: MyInteger = 5)
```

Conceptually:

```zax
myValue := (: MyInteger = 0) + (: MyInteger = 5)
```

The construction is real and is not guaranteed to be elided.

Ordinary direct candidates prevent fallback. If direct candidates accept
`U8` and `U16`, with no `Integer`, the source is ambiguous. If only the `U16`
candidate exists, it wins and the number realizes as `U16`. A direct ambiguity
or selected range failure never falls back to constructing `MyInteger`.

#### Phrase fallback forbids combination search

**Aligned finding:** Contextual phrase completion requires one operator tree and
one exact phrase component before applying the peer's type. The compiler never
uses a guessed receiver type to try multiple word partitions.

Eligible structure may be established by:

- a symbolic form;
- an otherwise structurally unique single-word phrase;
- an exact phrase fence;
- grouping that fixes the complete tree; or
- another future mechanism that provides the same certainty.

```zax
0 'foobar things make nice decorations' (: MyInteger = 5)
```

Fencing is not privileged for its own sake; it removes phrase-component
combination search. It may still need grouping because a fence does not by
itself choose the complete operator tree.

#### Contextual completion does not spread automatically

**Aligned finding:** A contextual constructor does not authorize every
compiler-known destination to insert it.

The initial participating contexts are:

- peer-directed operator completion for an operator that also says
  `contextual`;
- destination-directed plain `as` for an applicable contextual form; and
- language-provided fundamental integer realization.

Ordinary function arguments, returns, and conditional convergence do not gain
contextual construction for arbitrary identities. Future work may add a context
only through an explicit category-level decision using the same completion
vocabulary.

#### `as` follows the general contextual rule

**Aligned finding:** Plain `as` is not a resolver special case. Its parsed type
operand may act as the unique type anchor declared by a contextual operator
form:

```zax
myByte := 55 as U8
```

The contextual operation obtains `U8`, requires one contextual construction of
the receiver, and then performs the written concrete operation. This succeeds
only when sign intent and range permit `U8(55)`.

```zax
myError := 355 as U8 // error: 355 cannot contextually construct U8
```

A custom type participates by the same rule only when its construction and
applicable `as` form both say `contextual`.

Endian types need not make destination `as` contextual. Their current
destination-owned `from` operation preserves entry:

```zax
// Generated name remains illustrative.
myEndian := BigEndianU8 from 5
```

The lasting endian pressure is destination-owned admission whose exact backing
integer parameter can give uncommitted numeric source its type. Exact spelling
and generated naming may change without removing that capability.

#### Contextual construction precedes operator policy

**Aligned finding:** Contextual completion is exact, intent-respecting, and
policy-neutral. It constructs the receiver before optional, narrowing,
reporting, or other operator policy runs.

The protected optional and narrowing `as` forms should be `explicit`, not
`contextual`, because contextual construction would make their apparent policy
misleading and mostly redundant:

```zax
myOptional := 55 as U8? // error: optional `as` requires a concrete receiver
myNarrow := 55 narrowing as U8 // error: narrowing requires a concrete receiver
```

Use a concrete source:

```zax
myWide : I16 = 355
myOptional := myWide as U8? // absent
myNarrow := myWide narrowing as U8 // U8(99)
```

If a future custom optional or policy operator deliberately says `contextual`,
the same ordering still applies: failure to construct the receiver is an error,
not an inferred absence, narrowing, or report result. The selected operator's
policy begins only after the concrete receiver exists.

Sign intent is checked during contextual construction and is not superseded by
the later conversion policy:

```zax
myError := +55 as I8 // error: unsigned intent cannot construct I8
myValue := (: U8 = +55) as I8 // concrete U8 may convert to I8 when it fits
```

Optional conversion does not absorb an intent error.

#### Identity boundaries reset contextual posture

**Aligned finding:** `admit`/`restricted` and `expose`/`opaque` do not decide
contextual completion. Generated admission and exposed operations reset to
`explicit` at the new identity boundary. Contextual eligibility never crosses
automatically from the underlying type.

An identity owner may opt individual constructors, admissions, or operators
back into contextual completion:

```zax
// Illustrative future identity-body/completion syntax.
MyBarInteger :: identity admit expose type Integer {
  +++ contextual final : ()(rhs : Integer) = {
  }

  operator binary '+' contextual existing
}
```

The original identity declaration body may temporarily provide owner authority
before the identity is sealed. A later `partial` remains a separately governed
extension path.

#### Immediate underlying operation family

**Aligned finding:** Identities and owned components require a coherent
immediate-boundary family:

```zax
MyIdentity underlying type
myIdentity underlying value
myIdentity underlying place
```

- `underlying type` returns the immediate underlying type definition.
- `underlying value` returns one copied/projected underlying value.
- `underlying place` grants the defining authority direct access to the
  immediate underlying storage place under the applicable place, value, access,
  and lifetime permissions.

`underlying place` is preferred over `underlying reference` because it does not
prematurely promise a first-class reference, alias escape, or lifetime. A
function may bind an explicit reference from the place when permitted.

The three operations are boundary-sensitive and must not be forwarded
mechanically by `expose` or `own`. Each outer identity/component regenerates
them for its own immediate boundary:

```zax
((_ underlying place) underlying place).doSomething()
```

The second operation applies to the type reached through the first. Exact
public availability of `underlying type`/`underlying value`, owner-only
availability of `underlying place`, interaction with current `as
UnderlyingType` projection, and optional syntax for naming the underlying place
remain identity/owned-composition integration details.

For ordinary owner-defined work, use the exposed identity surface when it
suffices:

```zax
double final : ()() = {
  _ *= 2
}
```

Use `underlying place` only when the original owner deliberately needs the
contained storage:

```zax
doubleUnderlying final : ()() = {
  (_ underlying place) *= 2
}
```

This pressure belongs in `project/raw/owned-composition.md` and the current
identity owner.

#### Optional construction does not imply absence

**Aligned finding:** Constructing `T?` from a source succeeds only when the
selected present-value construction can establish a valid `T`. Constructor
ambiguity, intent conflict, range failure, transfer failure, or unavailable
construction is an error. Absence occurs only when the selected operation
explicitly requests or produces absence.

Ordinary concrete optional construction remains:

```zax
myValue : FooBar
myOptional := (: FooBar? = myValue)
```

Copy, move, consuming/`last`, contained lifetime, and failure cleanup are future
optional/lifetime details.

If `FooBar` has a contextual constructor from `Integer`, a present optional may
forward numeric source into that contained construction:

```zax
myOptional := (: FooBar? = 355)
```

Conceptually:

```zax
myOptional := (: FooBar? = (: FooBar = 355))
```

The implementation may construct `FooBar` directly in optional storage; the
expansion explains selection and failure rather than requiring a temporary.

For `U8`:

```zax
myOptional := (: U8? = 355) // error: 355 cannot construct U8
```

This is conceptually:

```zax
myOptional := (: U8? = (: U8 = 355)) // error: 355 is outside U8
```

The failure does not narrow to `U8(99)`, create absence, try another width, or
fall back to default optional construction.

Optional constructor overloads use ordinary numeric candidate preference before
range checking. A generated present-from-`U8` constructor selects `U8` and then
fails range. A separately declared `Integer` constructor would win the
`Integer` latch and define its own optional result behavior. `U8`/`U16`
constructors without `Integer` remain ambiguous; range does not select `U16`.

A concrete source works only through a declared applicable constructor:

```zax
myWide : I16 = 355
myOptional := (: U8? = myWide)
// error unless U8? declares construction from I16
```

Optional integer conversion remains distinct:

```zax
myOptional := myWide as U8? // absent
```

Complete optional constructor generation, copy/move/`last`, reset, unwrapping,
and in-place lifetime behavior remain future optional work. The aligned
constraint must be captured in the live optional input before this item closes.

#### Resolver stopping examples

**Aligned finding:** The number-literal resolver's complete responsibility is
to give every participating numeric source value one concrete type. It stops
for that expression node as soon as it has done so; ordinary typed compilation
then resumes.

```zax
myMathematicalResult : U8 = 200 + 55
myTypedResult := (: U8 = 200) + 55
myOtherResult := 200 + (: U8 = 55)
```

For number-literal resolution these are equivalent to:

```zax
myMathematicalResult : U8 = 255
myTypedResult := (: U8 = 200) + (: U8 = 55)
myOtherResult := (: U8 = 200) + (: U8 = 55)
```

The conceptual rewrites do not require source rewriting or anonymous
temporaries. The normal compiler subsequently selects, evaluates, or emits the
concrete `U8` operations. Stopping is the intended completion of the resolver,
not lazy or incomplete compilation.

In:

```zax
myByte : Byte = 42
```

the numeric source `42` is the uncommitted integer until the declaration gives
it type `Byte`.

In:

```zax
myInteger : Integer = compileTimeInteger()
myByte : Byte = myInteger // error: distinct integer identities
```

the function result and `myInteger` are concrete `Integer` values and are never
uncommitted.

#### Documentation guidance: invariants, outcomes, and techniques

**Aligned documentation finding:** Documentation guidance must distinguish:

1. **Hard invariants** protecting authority, correctness, and process, including
   authorization, one owner, staged-boundary preservation, required dry-run
   output, maturity, complete disposition, and no current dependency on raw or
   archived work.
2. **Reader outcomes** describing successful teaching, including direct-entry
   comprehension, ordinary use before advanced mechanisms, predictable source
   behavior/type/failure/cost, and terminology that adds precision rather than
   blocking entry.
3. **Suggested techniques** such as example before abstraction, plain language
   before a project term, local boundary followed by owner link, and moving
   advanced interaction after prerequisites.

Suggested techniques are diagnostic aids, not a required content template. An
agent must not add an example, definition, formal rule, or link merely to satisfy
a sequence. When techniques compete, preserve hard invariants and choose the
presentation that best achieves the reader outcome.

The direct-entry sequence recently added to `project/documentation.md` must be
recast as an often-effective technique rather than a mandatory five-step rule.
The focused correction should also inspect nearby human-facing guidance for
techniques accidentally phrased as universal process requirements. Dry-run,
authority, ownership, and review-boundary rules remain hard.

#### Required follow-up before closure

The new findings materially affect at least:

- `language/integer-literals.md`;
- `language/integer-operator-catalog.md`;
- `language/operator-catalog.md`;
- `language/operators.md`;
- `language/integers.md`;
- `language/construction-and-destruction.md`;
- `language/identity-types.md`;
- `language/endianness.md`;
- `language/terms.md`;
- `project/documentation.md`;
- `project/raw/partial-types.md`;
- `project/raw/owned-composition.md`;
- `project/raw/numeric-type-families.md`; and
- the live legacy/future optional input.

The exact updated promotion set must be established by a revised documentation
fit dry run rather than inferred from this preliminary list. No current owner,
raw input, legacy optional page, router, archive, or other file is edited by this
capture.

### Revised pre-promotion documentation fit dry run

#### Result

**PASS — 2026-09-01**

The continued findings have coherent current owners, live deferred
destinations, and an exact 18-file promotion set. No new document, directory,
or public route is required.

This PASS depends on a reader-first reconstruction. The dense continued-review
record is evidence and contract input, not candidate prose for distribution.
Promotion fails if it copies the working record into owner documents, preserves
its discussion order, or adds formal terminology before the programmer has a
concrete reason to care.

The current wider documentation remains incomplete until the revised promotion
is applied. PASS means the findings can be taught and integrated coherently; it
does not mean the currently staged owner text already contains them, and it
does not authorize promotion.

#### Teachability gate

The revised promotion must treat teachability as the presentation priority while
remaining technically complete and accurate.

`language/integer-literals.md` must be revised in two passes:

1. **Build the programmer's model.** Begin with ordinary programs:

   ```zax
   myShifted := 1 << 3
   myBits := 55 ^ 77
   myByte := 55 as U8
   ```

   Explain the numerical outcomes before naming width invariance or contextual
   completion.
2. **Trace the complete findings.** Add negative values, complement cases,
   failure distinctions, direct-before fallback, phrase fencing, declaration
   modes, optional construction, stopping, diagnostics, and deferred
   boundaries only after their prerequisites are established.

The owner should teach the width question in ordinary language before its
formal test:

> Would choosing another sufficiently wide integer type change the number this
> operation produces?

Only after examples answer that question should the document name the
width-invariance rule and provide the complete table.

Contextual completion should begin with the motivating contrast:

```zax
myByteResult := 0 + (: U8 = 5)
myIdentityResult := 0 + (: MyInteger = 5)
```

Explain the visible peer, real construction cost, and need for both declarations
to opt in before presenting the fallback stages or `contextual`/`explicit`
reference details.

The `as` section should begin with programmer intent:

```zax
myByte := 55 as U8
myOptional := 55 as U8? // error: optional `as` needs a concrete receiver
```

It should then explain that `as` follows the same declared contextual-completion
mechanism as other operators and receives no policy-specific exception.

Each satellite owner must state only the locally relevant behavior in plain
language, show one representative example where useful, and then link to the
complete owner. It must not reproduce the working record's matrices or fallback
algorithm.

The revised promotion must apply these outcome checks:

1. A programmer entering any changed section directly can identify the ordinary
   source case and outcome before encountering project-specific terminology.
2. The main owner reads as a lesson, not an audit ledger or compiler algorithm.
3. Reference tables follow the examples and mental model they summarize.
4. Terms such as *width invariance*, *contextual completion*, *type anchor*, and
   *underlying place* are defined through their effect on source before carrying
   technical weight.
5. Local handoffs explain why the rule matters there; links do not replace local
   teaching.
6. Technical completeness is checked separately after the reader-facing pass so
   no aligned corner case is lost for brevity.
7. No rule or example is added solely to satisfy a presentation checklist.

#### Revised ownership map

| Finding | Current owner | Local integration or deferred destination |
| --- | --- | --- |
| Width-invariance test and expanded uncommitted shift/bitwise behavior | `language/integer-literals.md` | Concrete policies remain in `language/integer-operator-catalog.md`; exact forms remain in `language/operator-catalog.md`. |
| Value/intent-dependent complement and the three failure categories | `language/integer-literals.md` | Concrete complement remains in the integer operator catalog; terminology receives concise definitions only when independently useful. |
| Resolver stopping after numeric source receives a type | `language/integer-literals.md` | `language/integers.md` and the integer operator catalog receive short concrete handoffs. |
| `contextual`/`explicit` completion mode and explicit default | `language/operators.md` for operator participation; `language/construction-and-destruction.md` for constructor participation | `language/terms.md` defines the shared mode; exact final keyword placement remains illustrative. |
| Direct-before peer-construction fallback | `language/operators.md` | Integer-specific teaching and examples remain cohesive in `language/integer-literals.md`. |
| No phrase combination search during fallback | `language/operator-phrases.md` | General operator fallback stays in `language/operators.md`; source fencing mechanics remain unchanged. |
| Plain contextual `as` and explicit-only optional/narrowing `as` | `language/integer-operator-catalog.md` | Exact form/anchor classification belongs in `language/operator-catalog.md`; programmer model belongs in the integer-literal owner. |
| Sign intent checked before contextual construction | `language/integer-literals.md` | Concrete later conversion remains in the integer operator catalog. |
| Identity contextual posture reset and original declaration-body authority | `language/identity-types.md` | General composition/sugar and filtering pressure remains in `project/raw/owned-composition.md`. |
| Immediate `underlying type`/`value`/`place` family and non-forwarding | `language/identity-types.md` | Exact form catalog updates in `language/operator-catalog.md`; unresolved owned-component/naming/lifetime pressure remains raw. |
| Authorized partial candidates may relatch selection | `project/raw/partial-types.md` until partial authority is designed | Current literal owner records the source-stability consequence without defining partial syntax. |
| Destination-owned endian admission accepts numeric source | `language/endianness.md` | Generated naming remains illustrative; exact backing integer supplies the input type. |
| Optional construction failure does not imply absence | `language/construction-and-destruction.md` | `optional.md` retains complete optional constructor, copy/move/`last`, reset, and lifetime input; integer-literal owner teaches only the numeric consequence. |
| Hard invariants versus reader outcomes and suggested techniques | `project/documentation.md` | Existing dry-run, authority, ownership, maturity, and staged-boundary rules remain hard. |
| Current `55 ^ 77` error examples superseded by width-invariant behavior | Applicable local owners | `language/function-invocation.md` and `language/core-flow-control.md` replace them with genuinely width-dependent inner operations. |

#### Exact revised promotion change set

The revised promotion changes exactly these 18 files.

1. **`language/integer-literals.md`** — reorganize rather than append. Teach
   width-independent operations, complement, contextual completion, `as`,
   optional construction, and stopping through progressive examples; place
   complete matrices, fallback stages, diagnostics, and deferred boundaries
   afterward.
2. **`language/integer-operator-catalog.md`** — distinguish expanded
   mathematical uncommitted forms from concrete policies; document contextual
   plain `as`, explicit-only optional/narrowing `as`, and concrete shift/bitwise
   behavior without duplicating the main lesson.
3. **`language/operator-catalog.md`** — update compact domain availability for
   `<<`, `>>`, `&`, `|`, `^`, `&~`, conditional complement, and `as`; add the
   exact immediate-underlying forms and their owner/visibility boundaries.
4. **`language/operators.md`** — teach completion mode, direct-before
   contextual fallback, one-step construction, type anchors, no fallback after
   ambiguity/range/unavailability, and no arbitrary spread. Lead with the
   `0 + (: MyInteger = 5)` use case before the formal stages.
5. **`language/operator-phrases.md`** — add the narrow rule that contextual
   completion requires one already formed tree and exact component; explain
   fencing as one way to avoid combination search, not as privileged syntax.
6. **`language/construction-and-destruction.md`** — define contextual versus
   explicit constructor participation after ordinary constructor selection;
   add the general optional present-construction boundary, failure behavior,
   and a handoff for deferred transfer/lifetime details.
7. **`language/identity-types.md`** — define posture reset, original
   declaration-body authority, immediate underlying type/value/place behavior,
   owner-only place access, and non-forwarding across identity boundaries.
8. **`language/endianness.md`** — add a plain numeric-source example showing
   why destination-owned `from` must remain able to accept its exact backing
   integer input; retain illustrative generated naming.
9. **`language/integers.md`** — identify `42`, rather than the function result,
   as the uncommitted source in the initialization example and hand expanded
   mathematical operation behavior to the literal owner.
10. **`language/terms.md`** — add concise, source-grounded definitions for
    width invariance, completion mode/contextual completion, type anchor, and
    underlying place without turning the terms page into a second behavior
    owner.
11. **`language/function-invocation.md`** — replace the now-valid `55 ^ 77`
    expected-result counterexample with a genuinely width-dependent inner form;
    retain the rule that arbitrary contextual construction does not spread into
    ordinary parameter binding.
12. **`language/core-flow-control.md`** — replace the now-valid conditional
    `55 ^ 77` counterexample while preserving final-arm-only integer
    convergence.
13. **`project/documentation.md`** — classify guidance as hard invariants,
    reader outcomes, or suggested techniques; recast the direct-entry sequence
    as one often-effective method; state that comprehension, completeness, and
    accuracy are the goals rather than checklist compliance.
14. **`project/raw/partial-types.md`** — preserve the concrete
    constructor/operator relatching example, provenance-neutral selection
    stance, compatibility consequence, and future authority/import pressure.
15. **`project/raw/owned-composition.md`** — preserve identity declaration-body,
    contextual exposure, immediate underlying place, non-forwarding,
    optional naming, qualification, and lifetime pressure.
16. **`project/raw/numeric-type-families.md`** — update custom integer-family
    pressure for width-invariant mathematical operations and contextual
    construction without granting it through ordinary identity storage.
17. **`optional.md`** — add a current-ownership note for the aligned
    present-construction/failure boundary and preserve the remaining legacy
    copy/move/`last`, reset, unwrapping, and lifetime evidence without presenting
    it as accepted.
18. **`project/work/011-integer-literals.md`** — record applied dispositions,
    revised promotion validation, exact deviations if any, and the resulting
    current documentation-fit status.

No other file belongs in the revised set:

- public routing already reaches the integer-literal owner;
- source structure already owns the applicable phrase-fence mechanics;
- declarations already explain typed number initialization;
- ordinary function/return/conditional contexts do not gain contextual
  construction for arbitrary identities;
- raw literal and compile-time inputs retain unaffected boundaries;
- raw indexes already reach partial, owned-composition, and numeric inputs;
- qualifier/place semantics already exist and can be linked without amendment;
- no legacy numeric-prefix example changes; and
- no archive or current-work pointer participates.

#### Deferred details after revised promotion

The following remain explicit future work:

- exact final grammar and keyword placement for `contextual`/`explicit`;
- whether future callable categories opt into contextual completion;
- complete optional copy, move, `last`, reset, unwrapping, contained lifetime,
  and failure cleanup;
- whether an identity declaration may name its underlying place in the header;
- precise alias/lifetime behavior of owner-only underlying place access;
- partial authority, import visibility, and compatibility/version policy;
- complete count/reduction behavior on uncommitted values;
- contextual policy forms for custom types beyond the protected integer choices;
- custom integer-family declaration syntax; and
- formal grammar, diagnostics identifiers, and compiler algorithms.

Each deferral has a current boundary and a live owner or raw/legacy input. None
invalidates the revised current model.

#### Revised promotion validation plan

The revised promotion must pass:

1. **Reader journey:** the main owner begins with ordinary valid examples and
   explains outcomes before mechanisms.
2. **No transcription:** no owner reproduces the continued-review chronology or
   copies its dense fallback/operation discussion as opening prose.
3. **Technical trace:** every aligned finding above appears in its mapped owner
   or deferred destination after the teaching pass.
4. **Width behavior:** current docs no longer reject `1 << 2`, `1 << (1 << 3)`,
   or `55 ^ 77`; concrete-count and width-dependent counterexamples remain
   correctly diagnosed.
5. **Complement:** unknown positive, unsigned, signed, and unknown-negative
   examples teach the deciding distinction without beginning with a matrix.
6. **Failure separation:** mathematical non-invariance, intent conflict, and
   final range failure remain distinct.
7. **Contextual fallback:** direct candidates always win; ambiguity, selected
   range failure, and unavailable-best results never trigger fallback.
8. **Phrase boundedness:** contextual completion never searches phrase
   combinations; fenced examples remain subject to complete-tree selection.
9. **Policy order:** contextual construction precedes operator policy; protected
   optional and narrowing `as` require concrete receivers.
10. **Identity boundary:** admission/exposure reset to explicit; underlying
    boundary operations are regenerated and not forwarded.
11. **Optional behavior:** failed contained construction never silently becomes
    absence or narrowing.
12. **Partial consequence:** admitted partial candidates rank without provenance
    preference and may relatch existing source.
13. **Endian entry:** destination-owned admission accepts number literals through
    its exact backing integer input.
14. **Guidance posture:** hard process rules remain exact, while teaching
    sequences are presented as techniques serving explicit reader outcomes.
15. **Authority:** illustrative future syntax is marked; current conceptual
    behavior is not described as implemented or formally specified.
16. **Owner boundaries:** current owners contain no dependency on raw or archived
    work and no duplicate complete model.
17. **Examples:** known failures carry source comments and prerequisites appear
    before use.
18. **Navigation:** all local links and anchors resolve in repository and website
    Markdown.
19. **Markdown:** code fences and whitespace checks pass.
20. **Scope:** the final revised promotion changes exactly the 18 files above.
21. **Review boundary:** the maintainer's staged baseline remains unchanged until
    separately authorized.

#### Revised dry-run conclusion

The continued findings can be promoted without a new owner or unresolved
architecture decision. The exact owner set, deferred destinations, reader-first
teaching plan, and validation obligations are coherent. The revised
pre-promotion documentation fit dry run therefore **PASSES**.

Wait for separate revised promotion authorization.

#### Revised promotion result

**PROMOTED AND VALIDATED — 2026-09-01**

The language maintainer separately authorized the exact 18-file revised
promotion set. Every planned file was applied with no added, omitted, moved, or
renamed path.

Promotion rebuilt the main integer-literal teaching rather than appending or
copying the continued-review record:

- ordinary shift, bitwise, complement, contextual construction, `as`, optional,
  and stopping examples establish the reader's model first;
- width invariance, completion modes, fallback stages, failure distinctions,
  and reference details follow those examples;
- satellite owners contain only the source case and local boundary needed by
  their readers;
- the complete technical findings remain traceable in later layers without
  preserving discussion chronology.

The revised owner integration:

- expands uncommitted mathematical operations through the width-invariance
  test;
- gives conditional complement behavior distinct value/intent cases;
- defines `contextual`/`explicit` completion and direct-before fallback;
- prohibits phrase combination search during contextual completion;
- treats plain `as` through the general contextual mechanism while keeping
  optional/narrowing `as` explicit;
- resets identity completion posture, defines immediate underlying
  type/value/place operations, and prevents their forwarding;
- preserves destination-owned endian admission for number literals;
- makes optional present-construction failure distinct from absence;
- preserves authorized partial relatching as a future partial compatibility
  constraint; and
- clarifies exactly when the number-literal resolver stops.

Documentation guidance now distinguishes hard invariants, reader outcomes, and
suggested techniques. The earlier direct-entry sequence is an often-effective
method rather than a mandatory content template; completeness and accuracy
remain required outcomes.

Validation passed:

1. the revised promotion changes exactly the 18 dry-run files;
2. the staged 25-file baseline remains unchanged;
3. staged and unstaged whitespace checks pass;
4. all revised links and heading anchors resolve;
5. all revised Markdown code fences balance;
6. current language owners contain no raw/project dependency;
7. no current document still rejects `1 << 2`, `1 << 3`, or `55 ^ 77`;
8. complement examples distinguish unknown positive, unsigned, signed, and
   unknown-negative operands;
9. mathematical non-invariance, intent conflict, and final range failure remain
   separate;
10. direct ambiguity, selected range failure, and unavailable-best operations
    never trigger contextual fallback;
11. phrase completion requires one established component/tree and never tries
    word combinations;
12. optional and narrowing integer `as` require concrete receivers;
13. underlying boundary operations regenerate rather than forward;
14. failed contained optional construction never becomes absence or narrowing;
15. illustrative future syntax is marked and current behavior is not described
    as implemented or formally specified; and
16. the main owner passes the revised reader-journey and no-transcription tests.

No deviation requires another dry run. **Current documentation-fit status:
PASS.**

Closure, archival, work item `012`, staging the revised promotion layer,
committing, and pushing remain separate actions requiring their own
authorization.

#### Direct teaching and underlying-access follow-up

**PROMOTED AND VALIDATED — 2026-09-01**

Maintainer feedback aligned a bounded follow-up after the revised promotion.
The maintainer authorized direct integration without another dry run because
the changes preserve the established owner structure and do not reopen the
integer-resolution model.

The follow-up changes exactly these 11 files:

1. `language/construction-and-destruction.md`;
2. `language/core-flow-control.md`;
3. `language/endianness.md`;
4. `language/identity-types.md`;
5. `language/integer-literals.md`;
6. `language/integer-operator-catalog.md`;
7. `language/operator-catalog.md`;
8. `language/terms.md`;
9. `project/raw/enum-types.md`;
10. `project/raw/owned-composition.md`; and
11. `project/work/011-integer-literals.md`.

Teaching corrections:

- contextual constructors now show one successful and one failed use;
- conditional complement shows the signed/unsigned results that prevent an
  unknown positive operand from resolving;
- endian numeric admission and contextual construction use real anonymous typed
  declaration syntax instead of pseudo-constructor notation;
- completion-mode terminology identifies constructor/operator candidate
  selection explicitly; and
- identity teaching introduces the original-definition-open phase and sealing
  before relying on contextual or underlying operations.

Underlying-access integration:

- an identity is open only during its original compile-time definition body and
  seals when the body closes;
- the body does not introduce inheritance or a runtime partially defined type;
- `underlying place` is a private final post-unary operation for the immediate
  identity defining authority;
- it is regenerated rather than forwarded and preserves the receiver path's
  qualifications and authority;
- nested teaching uses copied/projected `underlying value` rather than implying
  automatic private access through another identity;
- a second private place boundary requires independent private eligibility or an
  operation deliberately published by the inner owner; and
- enums do not currently receive `underlying place`.

The enum raw input now preserves original enum definition-body pressure with
format/parse examples, closing-brace sealing, no added instance storage or
inheritance, generated-behavior replacement questions, and a possible private
backing-place capability for future enum owner bodies. It also uses the current
operator-catalog heading.

Validation passed:

- the staged baseline remains unchanged;
- the direct follow-up contains exactly the 11 files above;
- staged and unstaged whitespace checks pass;
- all follow-up links, heading anchors, and code fences resolve/balance;
- current promoted prose contains no numeric pseudo-constructor notation;
- `underlying place` is absent from the current generated enum surface;
- enum/private-place pressure remains clearly raw and non-authoritative; and
- current documentation-fit status remains **PASS**.

No closure, archive, `012`, staging, commit, or push action is included.
