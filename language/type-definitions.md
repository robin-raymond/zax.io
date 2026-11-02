# Zax type definitions

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers defining, constructing, composing, or inspecting Zax types |
| Applies To | Ordinary named and anonymous type definitions and their programmer-visible organization; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The ordinary type-definition mental model; named and anonymous identity; direct stored shape; type-body storage, behavior, and metadata categories; incomplete self-names, forwards, recursion, completion, and `outer tracked` integration; explicit member layout with `align`, `reorder`, `reserve`, and `void` |
| Does Not Own | General declaration syntax ([declarations and bindings](declarations-and-bindings.md)); complete lifecycle behavior ([construction, replacement, and destruction](construction-and-destruction.md)); qualifiers ([qualifiers](qualifiers.md)); composition ([Zax composition](composition.md)); structural compatibility ([structural shapes and compatibility](structural-shapes-and-compatibility.md)); [unions](unions.md); or [variants](variants.md) |
| Source / Provenance | Reviewed legacy type-definition evidence reconciled with current declaration, lifecycle, lifetime, qualifier, composition, structural, callable, union, and variant design |

## Start with one identity and one stored shape

```zax
MyPoint :: type {
  x : I32
  y : I32

  translate final : ()(
    dx : I32,
    dy : I32
  ) writable = {
    _.x += dx
    _.y += dy
  }
}

myPoint : MyPoint
myPoint.translate(3, 4)
```

`MyPoint :: type { ... }` introduces one named type identity. Each `MyPoint`
instance directly stores `x` followed by `y`. The `translate` function belongs
to the type but does not add a callable field to each point: `final` fixes one
implementation.

Inside an instance function, `_` is the
[current instance](terms.md#current-instance). Direct member access remains
explicit:

```zax
_.x = 10
x = 10 // error unless an ordinary lexical `x` is declared
```

This yields the central model:

> A type definition owns one identity and a declaration body. Only declarations
> that actually store per-instance values contribute to its direct stored
> shape.

## What a type body can contribute

### Direct stored members

An ordinary value declaration directly in the body creates one place in every
instance:

```zax
MyRecord :: type {
  id : U64
  label : String
}
```

Declaration order is observable. It controls direct shape, automatic
construction order, and reverse automatic destruction order.

An inferred member remains stored:

```zax
MySize :: type {
  width := 0
  height := 0
}
```

Initializers and defaults affect construction. They do not become additional
shape.

### Fixed and varying callable declarations

A `final` callable has one fixed implementation and no replaceable per-instance
slot:

```zax
MyCounter :: type {
  value : Integer

  increment final : ()() writable = {
    ++_.value
  }
}
```

A directly declared varying callable does have per-instance callable storage:

```zax
MyHandler :: type {
  callback : ()() bound
}
```

Its slot capacity, installed target, receiver relationship, reset, and transfer
behavior are defined by
[lambdas and callable composition](lambdas-and-callable-composition.md#callable-prototypes-and-storage).

An `unbound` function may belong to the type without receiving an instance:

```zax
MyMath :: type {
  twice final : (
    result : Integer
  )(
    value : Integer
  ) unbound = {
    return value * 2
  }
}
```

A `once` declaration belongs to the complete type rather than adding ordinary
storage to every instance. This includes callable slots and any value category
for which `once` storage is available. Exact global/`once` value initialization,
concurrency, and teardown remain future lifecycle work.

Receiverless and instance-qualified `once` calls retain the Nothing-instance
behavior defined by
[Zax Nothing instances](nothing-instances.md#receiverless-and-instance-calls).

### Definitions, routes, and metadata

Nested types, transparent aliases, reshapes, abstract roles, fulfillments,
data routes, preferred routes, exposure metadata, and fixed operators do not
become direct stored members merely because they appear in the body:

```zax
MyContainer :: type {
  MyIndex :: alias type U32

  payload : MyPayload
  label via payload.label

  inspect final : ()() readonly = {
    print(_.label)
  }
}
```

Each declaration category follows its own owner. Future reflection must keep a
physical stored member distinct from an alternate path, generated wrapper,
fixed implementation, type-owned slot, or metadata-only declaration.

## Named identity is distinct from equal shape

Two separately named types remain distinct even when their stored members look
the same:

```zax
PixelPoint :: type {
  x : I32
  y : I32
}

KilometerPoint :: type {
  x : I32
  y : I32
}

pixel : PixelPoint
kilometers : KilometerPoint

pixel = kilometers // error: distinct identities
```

An explicit compatible construction or transformation can cross an accepted
structural relationship. Matching storage never makes the identities
interchangeable by default. Complete behavior is defined by
[structural shapes and compatibility](structural-shapes-and-compatibility.md).

A transparent alias adds another name for one identity:

```zax
ScreenPoint :: alias type PixelPoint
```

No conversion is required between `ScreenPoint` and `PixelPoint`.

## Anonymous type definitions

An ordinary value can define an anonymous type at its declaration:

```zax
myPair : :: type {
  left : Integer
  right : Integer
}
```

This declaration creates one stable compile-time identity. Inferred copies reuse
that identity:

```zax
another := myPair
MyPairType :: alias type type of myPair
named : MyPairType = myPair
```

Equal anonymous source written at another declaration does not by itself create
the same identity. Current design establishes no anonymous recursive type
syntax.

The specialized anonymous forms follow the same identity rule:

```zax
bits : :: union {
  byte : U8
  word : U32
}

choice : :: variant {
  text : String
  number : U32
}
```

Their storage and lifecycle behavior belongs to [unions](unions.md) and
[variants](variants.md). Category-specific aliases preserve those identities:

```zax
MyBits :: alias union type of bits
MyChoice :: alias variant type of choice
```

## Self-reference, recursion, and completion

A named type's own incomplete name is visible while its body resolves:

```zax
MyNode :: type {
  value : Integer
  next : MyNode * unique
}
```

The pointer representation is finite. Its default state is vacant, and its
ownership contract governs any later pointee.

Direct recursive storage has no finite layout:

```zax
MyBrokenNode :: type {
  next : MyBrokenNode // error: infinitely recursive stored layout
}
```

The same rule applies through several types. Use a finite indirection wherever
a recursive cycle would otherwise require inline storage.

The self-name supplies identity before completion, but facts requiring the final
member set remain pending:

- size and alignment;
- direct and flattened shape;
- generated lifecycle operations;
- constructor viability;
- union admissibility;
- variant payload extent; and
- reflection of the completed declaration.

### Forward anchors

`forward type` establishes a source-ordered name and category before the
definition appears:

```zax
MyOtherNode :: forward type

MyNode :: type {
  next : MyOtherNode *
}

MyOtherNode :: type {
  next : MyNode *
}
```

The forward supplies no body, layout, members, or initialization behavior. It
must receive exactly one matching completion. A type does not need `forward`
merely to use its own self-name.

Complete path and forward behavior belongs to
[declarations and bindings](declarations-and-bindings.md#forward-anchors).

Specialized definitions preserve their own forward categories:

```zax
MyOverlay :: forward union
MyChoice :: forward variant
```

Completion must be a matching direct declaration or an exact alias to an
existing identity of that declaration category.

## Construction and member lifetime

For ordinary direct members:

1. automatic construction follows declaration order;
2. explicit constructor-body member operations may choose another tracked
   control-flow order;
3. a constructor must normally complete every required member;
4. the enclosing destructor body runs while remaining members are live; and
5. automatic member destruction follows reverse declaration order.

```zax
MySession :: type {
  connection : MyConnection
  log : MyLog

  +++ final : ()(
    endpoint : MyEndpoint
  ) = {
    _.connection.+++(endpoint)
    // log uses its ordinary automatic construction.
  }
}
```

Complete replacement renews every member resident instance even when a custom
replacement constructor retains a resource or leaves bytes at the same address.
See
[construction, replacement, and destruction](construction-and-destruction.md)
and [lifetimes and references](lifetimes-and-references.md).

## Qualifications follow the actual path

The containing value, each direct member, and every pointer, reference, or
wrapper layer retain their resolved qualifications:

```zax
record : MyRecord
reader : MyRecord readonly & = record

record.label = "changed"
reader.label = "blocked" // error: member path is readonly
```

Deep immutability follows direct structural containment. It does not silently
rewrite pointee, referent, optional payload, or variant payload qualifications.
Complete behavior belongs to [Zax qualifiers](qualifiers.md).

## Composition preserves physical containment

```zax
MyEngine :: type {
  rpm : Integer

  start final : ()() = {
  }
}

MyCar :: type {
  engine own expose : MyEngine
  wheels : Integer
}

myCar : MyCar = [{
  .engine = makeMyEngine()
}]

myCar.rpm = 2000 // published path to engine.rpm
myCar.start()    // exposed forwarding behavior
```

`myCar.rpm` and `myCar.engine.rpm` reach the same stored member place.
`myCar.start()` is generated behavior. Neither relationship flattens physical
construction or destruction: `.engine` remains the direct construction-packet
entry, and `MyEngine` remains one complete contained value.

Complete publication, projection, forwarding, and role behavior belongs to
[Zax composition](composition.md).

## `outer tracked` is an explicit type capability

```zax
MyTrackedEngine :: type outer tracked {
  rpm : Integer
}

MyTrackedCar :: type {
  engine : MyTrackedEngine
}

recoverMyCar final : (
  result : MyTrackedCar & ?
)(
  engine : MyTrackedEngine &
) = {
  return engine tracked outer cast MyTrackedCar.engine
}
```

`outer tracked` permits hidden representation and lifecycle bookkeeping that
describes the current resident instance's immediate placement. A standalone
engine and an engine stored at `MyTrackedCar.engine` have different
relationships.

Construction, copy, move, and replacement establish destination-correct
tracking. Raw bit relocation is not sufficient. The capability changes shape,
cost, and compatibility even though its hidden component is not an ordinary
source-declared member.

Complete outer-cast behavior and costs belong to
[Zax composition](composition.md#outer-casting-to-an-immediate-container).

## Unions and variants are specialized type definitions

An unmanaged [union](unions.md) declares several typed lenses over one untagged
backing representation. Lenses are not ordinary resident members and have no
individual construction or destruction.

A managed [variant](variants.md) declares several named alternatives while
allowing zero or one payload life path to be active. Its wrapper owns selection,
construction, destruction, and replacement.

They intentionally do not share one “active member” model.

## Explicit member layout

Usually the compiler decides how members are aligned and how much padding sits
between them, while keeping members in declaration order. When a layout must
match something outside the program, such as a network packet, a file format,
or a hardware register block, directives state it explicitly:

```zax
MyPacketHeader :: type {
  size : U32

  [<align=16>]
  id : Uuid                    // placed at an offset that is a multiple of 16

  [<reserve=100>]              // 100 unnamed bytes at this point

  [<void>]
  checksumView : U64           // has an offset here, but occupies no bytes

  checksum : U32               // shares checksumView's offset
}
```

### Alignment

`[<align=n>]` on the line above a member places that member at an offset that is
a multiple of `n`. `n` must be a power of two; any other value is an error.

### Member order

Members are never reordered unless the type asks. `[<reorder=compact>]` or
`[<reorder=speed>]` above a type definition lets the compiler choose an order
that minimizes size or favors access speed; `reorder=none` is the default. A
reorder changes only where members are placed. Construction and destruction
still follow declaration order.

```zax
[<reorder=compact>]
MyStats :: type {
  enabled : Boolean
  total : U64
  flags : Byte
}
```

### Reserved bytes

An unattached `[<reserve=n>]` inside a type body adds `n` unnamed bytes at that
point. Reserved bytes have no initial value and carry no value, like the padding
described for [unions](unions.md#padding-and-non-value-bits): copying, equality,
and construction never depend on their contents.

### Members that occupy no space

`[<void>]` above a member gives that member an offset but no size. The next
member starts at the same offset. Reading a void member reinterprets whatever
bytes are at its offset, including later members and possibly bytes beyond the
type's own storage, such as variable-length data that follows a header in
memory.

That reinterpretation is unchecked. Writing `[<void>]` is itself the visible
promise that the access is valid, as with other
[unchecked-promise directives](compiler-directives.md#what-a-directive-may-promise),
so no separate `unsafe` marker is required.

### Layouts that differ by environment

When a layout must differ between targets, select it while compiling and mark
the selected member [`outward`](declarations-and-bindings.md#outward-declarations):

```zax
MyBlock :: type {
  if compile size of Target.Integer > 4 {
    [<align=16>]
    data outward : MyPayload
  } else {
    data outward : MyPayload
  }
}
```

### Layout and partials

By default, [partials](partials.md#what-a-type-allows-others-to-add) cannot
add stored members, so an explicit layout stays as written. A type that writes
`seal open storage` lets partials append storage after its own members.
`reserve` and `void` describe positions among the type's own members, so they
keep their meaning, but the total size grows. A layout whose total size must
stay fixed simply leaves storage closed.

## Partial and generic completion boundaries

Another declaration can add to a type through a
[partial](partials.md). A type states what others may add with a `seal`
clause:

```zax
MyCatalog :: type seal open storage {
  entries : Integer
}
```

By default, others may add functions, nested types, and `once` state, but not
stored members. `seal open storage` allows added stored members, and bare
`seal` allows nothing.

Added stored members change the type's size, layout, and generated lifecycle,
so those facts wait until every storage addition is known. For a type that
opens storage, that happens when its own module is finalized; importers see
the final layout as soon as the import completes. A type with
`seal open storage module` waits until the whole application is assembled.
Until then, `size of` and the other completion facts listed under
[self-reference, recursion, and completion](#self-reference-recursion-and-completion)
remain pending.

A partial never adds union lenses or variant alternatives. Complete partial
behavior, including which categories exist and how storage is included, belongs
to [Zax partials](partials.md).

Likewise, a generic instantiation must become one complete concrete type before
size, recursive layout, generated lifecycle operations, union admissibility, or
variant storage and coverage are decided. Exact generic syntax remains future
work.

## Costs and diagnostics

Programmers must be able to discover:

- direct stored size, alignment, padding, and hidden structural components;
- automatic initialization and destruction of every direct member;
- allocation, reference counting, or user code performed by member lifecycle;
- per-instance varying callable storage versus fixed implementations;
- type-owned `once` state;
- composition wrappers and tracking costs; and
- source-compatibility effects from member, qualification, or operation-family
  changes.

Diagnostics should distinguish:

- identity mismatch from structural incompatibility;
- infinite recursive storage from valid finite indirection;
- incomplete identity use from a request requiring completed layout;
- missing or duplicate forward completion;
- stored member declarations from no-storage definitions;
- implicit instance-member lookup without `_.`;
- unavailable generated lifecycle caused by one member path; and
- hidden `outer tracked` representation from ordinary visible data.

## Source stability and maturity

Changing a stored member's name, order, type, qualification, or lifecycle can
change layout, construction, destruction, compatibility, reflection, and
generated operations. Adding a fixed function does not change stored shape;
adding varying per-instance callable storage does.

This document defines current conceptual design, not formal grammar, ABI,
reflection schema, compiler lowering, or a conformance specification.
