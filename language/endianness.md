# Zax endianness

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax code that stores, transmits, or calculates with byte-ordered scalars |
| Applies To | Intrinsic integer, fixed-point, and floating-point endianness; not a formal grammar, ABI, wire contract, or specification |
| Implementation State | Not established by this repository |
| Owns | The endian mental model; concrete little/big specialization; active, compiler-host, and target selection; absolute names; numeric endian conversion; byte-preserving representation reinterpretation; cross-family operation behavior; storage rules; costs, diagnostics, and portability |
| Does Not Own | Integer semantics ([integers](integers.md)); fixed-point semantics ([fixed-point scalars](fixed-point-scalars.md)); floating formats ([floating-point scalars](floating-point-scalars.md)); exact forms and shared selection ([operator catalog](operator-catalog.md), [operators](operators.md)); aggregate compatibility ([structural shapes and compatibility](structural-shapes-and-compatibility.md)); or ABI/serialization contracts |
| Source / Provenance | Legacy [basics](../basics.md) endian conversion evidence, reconstructed against current scalar, operator, qualifier, and structural design |

## Mental model

> For every operation that an endian type supports, behave as though the value
> were being operated on natively by an environment with that byte order.

Endianness is an intrinsic property of each concrete integer, fixed-point, and
floating-point specialization. It is not a wrapper enum or per-value runtime
flag.

```zax
myBig : Scalars.Integers.Big.U32 = 4660
myFlag : Scalars.Integers.Big.U32 = 2147483648

myFlagged := myBig | myFlag
// Numeric result 0x80001234 in the same big-endian U32 identity.
```

The scalar family supplies the same applicable operation surface regardless of
whether the declared byte order matches the CPU. A mismatch can add conversion
or software cost but does not change numeric meaning.

## Concrete endian specializations

Every intrinsic scalar specialization resolves one concrete endianness:

```zax
myLittleInteger : Scalars.Integers.Little.U32
myBigInteger : Scalars.Integers.Big.U32

myLittleFixed : Scalars.Fixed.Little.I16F8
myBigFloat : Scalars.Floating.Big.Binary32
```

The endianness dimension participates in intrinsic type identity. Equal value
width and equal numeric format do not make little- and big-endian
specializations interchangeable.

A selector described as native or "agnostic" can appear while a profile or generic
factory is choosing a type. It resolves to `Little` or `Big` before the concrete
specialization exists. No instance changes endianness at runtime.

## Active and absolute names

The scalar-family root is the canonical current-execution-environment route:

```zax
Scalars.Integers.U32
Scalars.Fixed.I16F8
Scalars.Floating.Binary32

myInteger : U32
myFixed : I16F8
myFloat : Binary32
```

Explicit environment paths select compiler-host or target behavior:

```zax
myTarget : Scalars.Integers.Target.U32
myCompilerHost : Scalars.Integers.CompilerHost.U32
```

Use absolute paths when stored byte order must remain fixed independently of
the active environment:

```zax
myWireValue : Scalars.Integers.Big.U32
myFileValue : Scalars.Floating.Little.Binary32
```

Short absolute aliases may be supplied by later import/module work. The
canonical paths above do not depend on generated enum families.

## Numeric conversion and raw representation

Numeric conversion between endian specializations preserves represented value
and may rearrange bytes:

```zax
myLittle : Scalars.Integers.Little.U32 = 4660
myBig := myLittle as Scalars.Integers.Big.U32
// Both values mean 4660; their stored byte order differs.
```

Equal-format endian conversion is exact for every source value and therefore
does not panic, overflow, or underflow.

There is no implicit transfer:

```zax
myBig = myLittle // error: distinct endian identities
```

Representation extraction instead preserves logical encoding or storage bytes,
depending on the selected representation operation. Exact source words for
general scalar representation extraction and adoption remain future operator
work.

Coercive conversion is a local zero-translation view. Cross-endian coercion
preserves bytes and deliberately changes their interpreted value:

```zax
myReinterpreted :=
  myLittle as coercive layout
    Scalars.Integers.Big.U32 &
```

Complete conversion, representation-adoption, and coercion boundaries are
defined by the applicable scalar owner and
[structural shapes and compatibility](structural-shapes-and-compatibility.md);
`unsafe cast` is defined by [conversions and casts](casting.md).

## Native, compiler-host, and target endianness

**Native endianness** means the active byte order of the environment whose
execution semantics are currently in effect. Distinguish it from:

| Term | Meaning |
| --- | --- |
| Native endianness | The byte order of the environment whose execution semantics are currently active |
| Compiler-host endianness | The byte order of the environment running the compiler |
| Target endianness | The byte order selected for generated target behavior |
| Big endian / little endian | Absolute representation orders, independent of any environment |

At ordinary target runtime, native endianness is the target execution
environment's byte order. During compile-time execution, native endianness is the
byte order of the environment whose execution semantics are currently active.

Do not use `host`, `target`, and `native` interchangeably. An active-endian alias
is context-relative by construction.

## Operations use one endian identity

Endian specializations receive the complete applicable operation surface of
their scalar family:

- integers use the [integer operation catalog](integer-operator-catalog.md);
- fixed-point values use [fixed-point scalar operations](fixed-point-scalars.md);
  and
- floating values use [floating-point scalar operations](floating-point-scalars.md).

Operands normally share one exact identity:

```zax
myLittle : Scalars.Integers.Little.U32 = 1
myBig : Scalars.Integers.Big.U32 = 2

myInvalid := myLittle + myBig
// error: convert one operand to the intended endian identity

mySum := (myLittle as Scalars.Integers.Big.U32) + myBig
```

An uncommitted integer filling an operand of a selected endian identity receives
that same concrete endianness:

```zax
myBits : Scalars.Integers.Big.U32 = 1
myResult := myBits ^ 1024
// `1024` is realized directly as Scalars.Integers.Big.U32.
// The result retains that same type.
```

The compiler may calculate in a CPU-native register, swap around an operation,
select an endian-aware instruction, or call software. It must preserve the
declared logical width, numeric format, endian storage, operation result, and
failure behavior.

## Compatibility is not conversion

Two endian specializations can have the same storage extent while retaining
different scalar formats and identities. Concrete endianness is meaning-bearing:
active `U32` and `Scalars.Integers.Big.U32` do not become safely
shape-compatible merely because one target happens to be big-endian.

Storage coincidence describes representation. A conversion rule grants
transfer and preserves or changes numeric meaning. Neither representation
coincidence nor structural shape permits:

```zax
myBig : Scalars.Integers.Big.U32 = 1
myLittle : Scalars.Integers.Little.U32 = myBig
// error: structural compatibility is not a conversion rule
```

An assignment to unqualified `U32` succeeds when active `U32` resolves to the
same big-endian specialization and fails when it resolves to little-endian.

Use numeric `as` conversion to preserve value, a representation operation to
extract or adopt encoding, and local coercion to preserve bytes while changing
their interpretation. General shape, layout, scalar-format, posture, and
coercion terminology is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#scalar-compatibility).

## Costs

- Numeric conversion may cost nothing when orders match and a byte permutation
  or wider operation when they differ.
- Same-identity arithmetic may need swaps around a CPU-native operation.
- Raw representation extraction/adoption avoids numeric conversion but carries
  format and portability constraints.
- Coercion can be runtime-free; its unsafe form adds validity or restoration
  responsibility.
- Profile-selected active, target, and compiler-host types can differ and may
  require separate specialization.

## Worked example

Reading a big-endian length field out of a buffer and using it natively:

```zax
BigU32 :: alias type Scalars.Integers.Big.U32

length : BigU32 = readBigU32(buffer, offset)
flagMask : BigU32 = 2147483648 // hexadecimal 80000000
withoutFlag := length &~ flagMask

count : U32 = length as U32
writeBigU32(buffer, offset, withoutFlag)
```

On a little-endian active environment, numeric conversion and coercive
reinterpretation intentionally differ:

```zax
myBig : Scalars.Integers.Big.U32 = 1

myNumeric : U32 = myBig as U32
// numeric value 1

myStorageView :=
  myBig as coercive layout
    Scalars.Integers.Little.U32 &
// zero translation; the same bytes have another integer interpretation
```

## Diagnostics

Diagnostics should distinguish:

- mixed-endian operands requiring explicit conversion;
- numeric conversion from byte-preserving reinterpretation;
- active, target, compiler-host, and absolute endianness;
- unsupported arithmetic versus an endian adaptation cost;
- incompatible width, field placement, padding, or alignment for coercion;
- invalid representation adoption; and
- a runtime CPU mode that does not satisfy the selected target contract.

## Boundaries and maturity

This document is current conceptual design, not a formal specification, layout or
ABI contract, serialization framework, or implementation mapping.

Exact intrinsic generic syntax, profile files, representation-operation words,
ABI correspondence, serialization, and complete compile-time-context selection
remain focused future work.

Exact operator forms, fixity, and precedence are in the
[operator catalog](operator-catalog.md#endianness-reference). The shared operator
selection model is owned by [operators](operators.md), and cross-cutting
vocabulary by [language-design terms](terms.md#native-endianness).
