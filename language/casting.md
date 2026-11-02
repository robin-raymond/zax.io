# Zax conversions and casts

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers converting values between types or reinterpreting memory |
| Applies To | Choosing among conversion, stance restatement, structural compatibility, container and member navigation, and unchecked reinterpretation; programmer-declared `as`; `unsafe cast`; pointer and integer conversion; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The overview of conversion and cast operations; programmer-declared `as` conversion; `unsafe cast` forms, vacancy, qualification, ownership-role, cost, and diagnostic behavior; conversion between pointers and pointer-representation integers |
| Does Not Own | Numeric conversion ([integers](integers.md), [fixed-point](fixed-point-scalars.md), [floating-point](floating-point-scalars.md)); admission and projection ([identity types](identity-types.md), [enums](enums.md)); stance restatement ([transfer stances](transfer-stances.md)); compatibility, coercion, and structural applications of `unsafe cast` ([structural shapes](structural-shapes-and-compatibility.md)); outer casting ([composition](composition.md)); interior ownership ([pointers](pointers-and-arenas.md)); dereference ([Nothing instances](nothing-instances.md)); exact forms ([operator catalog](operator-catalog.md)) |
| Source / Provenance | Legacy casting material from the retired root casting page, refined against current operator, structural, pointer, qualifier, and scalar design |
| Supersedes | The retired root casting page |

## Zax has no universal cast

Zax offers several operations where many languages offer one cast, because
speed, safety, and efficiency matter more here than one flexible spelling. You
choose the operation that matches your intent, from a free reinterpretation of
existing memory to a conversion that builds a new value. Each of these lines
asks a different question:

```zax
display := boiling as Fahrenheit            // Celsius chose to provide this conversion
myByte := myCount narrowing as U8           // the language's modular integer conversion
header : PacketHeader * =
  myBuffer unsafe cast PacketHeader *       // treat these bytes as a header; nothing is checked
```

The first line runs code that the `Celsius` type wrote. The second follows the
integer rules for keeping only the low bits. The third converts nothing at all:
it tells the compiler to treat existing memory as a different type, and the
programmer takes responsibility for that being valid.

Keeping these operations apart lets a reader see the cost and the risk of each
line. A conversion can run code. Restating a stance moves nothing by itself. An
unchecked reinterpretation carries `unsafe` in its spelling.

`as` is an ordinary operator word. Most `as` operations are operators that a
type chooses to declare. The language owns only a small set of exact `as ...`
forms, such as stance restatement and structural compatibility, plus the
language-provided conversions of its own scalar types.

## Which operation do I want?

| I want to... | Representative source | Taught by |
| --- | --- | --- |
| Convert a number to another numeric type | `myWide := myU16 as U32`, `myMaybe := myU16 as U8?` | [integers](integers.md#conversion-to-exact-intrinsic-integers), [fixed-point](fixed-point-scalars.md), [floating-point](floating-point-scalars.md#numeric-conversion) |
| Convert my type to another type | `display := boiling as Fahrenheit` | [Writing your own `as`](#writing-your-own-as) below |
| Wrap a value in an identity or enum type | `myCount := MyCount from myRaw` | [identity types](identity-types.md#admission), [enums](enums.md#generated-boundary-operations) |
| Hand a value to a consumer as a move | `consume(myBuffer as move)` | [transfer stances](transfer-stances.md) |
| Use compatible storage as another named type | `pixel : PixelPoint = kilometers as shape PixelPoint` | [structural shapes](structural-shapes-and-compatibility.md#protected-compatibility-conversions) |
| Get the container from a member | `car : Car & = engine outer cast Car.engine` | [composition](composition.md#outer-casting-to-an-immediate-container) |
| Own a member of a shared allocation | `engine : Engine * strong = inner car.engine` | [pointers](pointers-and-arenas.md#interior-pointers) |
| Take an address, or follow a pointer | `pointer : MyValue * = value`, `view : MyValue & = pointer.` | [declarations](declarations-and-bindings.md), [Nothing instances](nothing-instances.md#pointer-dereference-is-unchecked) |
| Store an address as an integer | `myAddress : UPointer = myPointer as UPointer` | [Pointers and integers](#pointers-and-integers) below |
| Reinterpret memory without checks | `header : PacketHeader * = myBuffer unsafe cast PacketHeader *` | [Unchecked reinterpretation](#unchecked-reinterpretation-with-unsafe-cast) below |

Taking an address needs no cast. A pointer initialized from a value takes that
value's address, and a trailing `.` follows a pointer to its target. Both rules
are explained where they are owned.

## Writing your own `as`

A type provides a conversion by declaring binary `as` with a type parameter
slot:

```zax
Fahrenheit :: type {
  degrees : Binary64
}

Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
    result .= [{
      .degrees = degrees * 9.0 / 5.0 + 32.0
    }]
  }
}

boiling : Celsius = [{
  .degrees = 100.0
}]

display := boiling as Fahrenheit
```

The left value's type owns the conversion, so `boiling as Fahrenheit` finds the
operator on `Celsius`. `DestinationType` is a
[type argument](operator-phrases.md#type-arguments-and-type-qualified-operators):
it names a type and has no runtime storage, lifetime, or evaluation.

The result starts unconstructed. `result .= [{ ... }]` constructs it from the
packet, just as the same packet would initialize a declaration. Writing
`result.degrees = ...` first would be an error, because ordinary `=` never
constructs. See
[constructing a result later](function-invocation.md#opt-in-result-initialization).

Write each declaration for one intended destination type. A type parameter slot
cannot yet restrict which types a caller may name, so the declaration above
would also be selected for some unrelated `DestinationType`. It would then fail
to compile if that type has no `degrees` member. Restricting accepted type
arguments is future generic work.

A declaration may name a pointer or reference type as its destination. The
result then follows the ordinary rules for
[returned references](lifetimes-and-references.md#returned-references). There
is no built-in `as MyType *`; initializing a pointer from a value already takes
its address.

A type may also declare longer phrases that begin with `as`, such as
`as json text`. They work, but a lint discourages them: the language may add
new exact `as ...` forms later, and a collision would change what existing
source means. Plain binary `as` with a type argument is always safe to declare
on your own types.

Words inside a phrase are phrase words, not keywords. A type may name an
operation `unsafe as` to warn its readers, but that word grants no unsafe
authority. The language's own unchecked reinterpretation is `unsafe cast`.

### Your types and the language's scalars

Integers, fixed-point, and floating-point conversions belong to the language.
Your type can still convert to and from them:

```zax
myCount := myTally as I32                  // MyTally declares its own `as`

myTally : MyTally = [{
  .count = myCount                         // build a MyTally by construction
}]

myId : UserId = UserId from myRaw          // an identity type admits its underlying value
```

A scalar value is the receiver of `myCount as MyTally`, so `MyTally` cannot
supply that conversion. Use construction instead, or an identity type's
admission.

A [partial](partials.md) does not solve this. A partial can add operators to
`I32`, but an `as` declaration accepts every destination type, so a partial
`as` on `I32` would also claim `myCount as I64`, which belongs to the language.
It becomes a practical route only once a declaration can restrict its
destination type.

## Unchecked reinterpretation with `unsafe cast`

`unsafe cast` tells the compiler to treat existing memory as another type
without converting or checking anything. The result is always a view, a pointer
or a reference, never a new value:

```zax
otherPointer := myPointer unsafe cast OtherType *     // pointer to pointer
valueView := myValue unsafe cast OtherType &          // a value's storage viewed as OtherType
referenceView := myReference unsafe cast OtherType &  // reference to reference
```

The cast verifies no compatibility, qualification, invariant, lifetime, bounds,
alignment, or layout guarantee. Those facts become the programmer's
responsibility, and a false claim has undefined consequences. The compiler
still rejects a reinterpretation it proves impossible, even when `unsafe` is
written.

Before reaching for `unsafe cast`, check whether a checked operation expresses
the same intent. The
[structural compatibility and coercion forms](structural-shapes-and-compatibility.md#coercive-reference-views)
prove layout and validity facts for related types, and a declared `as` performs
real conversion.

### New values come from construction

A cast produces no new value, so there is no by-value form. To obtain a new
value, form the view and construct from it:

```zax
copy := myValue unsafe cast OtherType      // error: unsafe cast produces a view
copy : OtherType =
  myValue unsafe cast OtherType &          // view the storage, then construct a copy
```

A by-value spelling could not say whether it meant a byte copy, a constructor
call, truncation, or the start of a new lifetime. Separating the view from
construction keeps each responsibility visible: the cast owns the unchecked
reinterpretation, and construction owns copying, resources, and the new
lifetime.

### Vacant pointers stay vacant

A vacant pointer converts to a vacant pointer of the destination type:

```zax
vacantPointer : MyType *
otherPointer := vacantPointer unsafe cast OtherType *
// ?otherPointer is false: still vacant
```

Each type may represent vacancy differently, because a vacant pointer targets
its own type's [Nothing instance](nothing-instances.md). Without this rule, the
result would point at `MyType`'s Nothing backing while claiming to be an
`OtherType` pointer, and a presence test would report it as present. A
non-vacant address is reinterpreted unchanged.

To keep a vacant pointer's exact raw representation instead, convert it to a
[pointer-representation integer](#pointers-and-integers) and back. That round
trip does not preserve vacancy.

### Qualification

The written destination type states the complete qualification of the result.
`unsafe cast` can therefore remove `immutable`, `readonly`, or `final`, or add
promises that no ordinary conversion allows:

```zax
myMutable : MyType = makeValue()

asReadonly : MyType readonly & = myMutable            // ordinary: readonly narrows one path
asImmutable : MyType immutable & = myMutable          // error: another path may still change it
trusted := myMutable unsafe cast MyType immutable &   // programmer asserts nothing will change it
```

`immutable` promises that the value never changes for its whole lifetime. A
mutable value cannot make that promise through an ordinary view, so adding it
needs `unsafe cast`.

When the only goal is to change qualification on the same type, prefer
[`unsafe pliable`](qualifiers.md#unsafe-pliable). It keeps the recorded
qualifications and bypasses them locally for one explicit path, while
`unsafe cast` creates a differently qualified view that later code cannot
distinguish from an ordinary one.

### Pointer ownership roles

For raw, `unique`, `unique shareable`, `strong`, and `weak` pointers,
`unsafe cast` changes only the pointee type. The change of ownership role, the
transfer stance, and every lifetime rule are exactly those of the ordinary
[pointer conversions](pointers-and-arenas.md#ownership-transitions):

```zax
myCar : Car * strong = makeCar()

shared : Truck * strong = myCar unsafe cast Truck * strong               // another strong owner
observer : Truck * weak = myCar unsafe cast Truck * weak                 // ordinary strong to weak
exclusive : Truck * unique = myCar as last unsafe cast Truck * unique    // ordinary unique-claim rules
```

A pointer with a control block (`strong`, `weak`, or `unique shareable`) still
disposes the allocation its control block records. Viewing a `Car` allocation
as a `Truck` does not make release destroy a `Truck`.

A blockless `unique` pointer may also change its pointee type. Whether that is
valid depends on the program; the compiler cannot tell, and the `unsafe`
spelling says so. Which type's destruction runs when a retyped blockless
`unique` releases is not yet specified. Code that retypes one takes
responsibility for correct disposal.

## Pointers and integers

An address can be stored as an integer and turned back into a pointer:

```zax
myAddress : UPointer = myPointer as UPointer               // observe the address
myRestored : MyType * = myAddress unsafe cast MyType *     // assert it is a valid MyType address
```

Converting a pointer to an integer is safe. Observing an address cannot break
memory safety. The integer carries no provenance, though, so it proves nothing
if it is later turned back into a pointer.

Converting an integer to a pointer is unchecked reinterpretation. The
programmer asserts that the address is valid for the destination type, suitably
aligned, and live for every use.

- The integer must match the pointer's memory domain: `UPointer` for ordinary
  pointers, `Near.UPointer` for near pointers, and `Far.UPointer` for far
  pointers. See
  [pointer-representation integer roles](integers.md#pointer-representation-integer-roles).
- A vacant pointer converts to its raw representation. That value is not
  portable, and converting it back produces a pointer that is not vacant.
- The difference between two addresses uses `PointerDelta`, the exact
  difference type of `UPointer`.

## Costs

- A declared `as` costs whatever its body does, including allocation or other
  resource work.
- `unsafe cast` performs no conversion work. Preserving vacancy may need one
  test of the source pointer. That test disappears when the source and
  destination use the same vacant representation, or when the compiler proves
  that the source is never vacant.
- `myPointer as UPointer` and integer-to-pointer `unsafe cast` copy an address.
- Every `unsafe` form adds audit cost: a reviewer must confirm the facts the
  compiler did not check, and the result may be less portable across targets.

## Diagnostics

Useful diagnostics include:

- by-value `unsafe cast`, with the view-then-construct correction;
- a reinterpretation the compiler proves impossible, even under `unsafe`;
- an ordinary view that tries to add `immutable` or remove a qualification,
  with `unsafe pliable` and `unsafe cast` shown as the explicit alternatives;
- a pointer-to-integer conversion whose integer role does not match the
  pointer's memory domain;
- a declared `as` selected for a destination its body cannot produce; and
- a lint for programmer-declared phrases that begin with `as`.

## Boundaries and maturity

These concerns remain future work:

- restricting which destination types one `as` declaration accepts;
- adding operators to the language's scalar types from programmer code;
- helpers that view raw memory as an array or iterator of bytes;
- pointer provenance, alignment, segment, and comparability rules beyond the
  conversions described here; and
- which type a retyped blockless `unique` destroys.
