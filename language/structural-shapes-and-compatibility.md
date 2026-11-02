# Zax structural shapes and compatibility

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers defining, converting, routing, or inspecting structurally related values |
| Applies To | Programmer-facing structural shape, compatible binary recasting, anchored regions, decomposition, recomposition, and transformation; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Type identity versus shape; direct and flattened stored shape; compatibility postures and type-alias posture overlays; source anchors; safe structural conversion; coercive structural conversion; structural applications of `unsafe cast`; same-storage compatible views; `>-`, `-<`, `-<>-`, exact `reshape` aliases, reshape forwarding, callable result/input reshape mapping, and callable exceptional-outcome reshape; composition data-path participation; scalar-format and anonymous-report integration; structural costs, diagnostics, and source stability |
| Does Not Own | Complete exceptional result handling and forwarding ([exceptional result flow](except.md)); complete generic constraints, reflection APIs, pointer provenance, scalar-family meaning ([integers](integers.md), [fixed-point scalars](fixed-point-scalars.md), [floating-point scalars](floating-point-scalars.md)), partial behavior ([partials](partials.md)), ABI/FFI contracts, complete `unsafe cast` behavior and general casting ([conversions and casts](casting.md)), or compiler lowering |

## Start with distinct identities

Two types can store the same members without meaning the same thing:

```zax
PixelPoint :: type {
  x : I32
  y : I32
}

KilometerPoint :: type {
  x : I32
  y : I32
}

drawPixels final : ()(
  point : PixelPoint readonly &
) = {
}

kilometers : KilometerPoint
drawPixels(kilometers) // error: PixelPoint was requested, not KilometerPoint
```

The compiler cannot infer whether matching integers represent pixels,
kilometers, account balances, or unrelated concepts. `PixelPoint` and
`KilometerPoint` therefore retain distinct identities and remain
non-interchangeable by default.

Their direct stored shapes nevertheless match: both contain `x` followed by `y`,
and both members have the same complete `I32` type. Shape is a fact about their
structure. It is not permission to cross the identity boundary.

A programmer can request that crossing explicitly:

```zax
pixelPoint : PixelPoint =
  kilometers as shape PixelPoint
```

This protected operation verifies compatible shape and constructs one real
`PixelPoint`. It does not rename `KilometerPoint`, erase either identity, or
make every similarly shaped value interchangeable.

## Five questions that must remain separate

Structural code becomes predictable when it keeps these questions distinct:

1. **Identity:** What exact type does this value have?
2. **Stored shape:** Which resident members compose the value, in what order,
   and with which complete types and qualifications?
3. **Layout:** Where are those members represented, with what size, alignment,
   padding, and hidden state?
4. **Permission:** May this source be recast, copied, viewed, decomposed,
   recomposed, or transformed into the requested destination?
5. **Conformance:** Does the complete type satisfy a future no-storage
   requirement for values, callables, operators, or other declarations?

The first three describe the value. Permission is supplied by source posture or
an explicit operation. Whole-type conformance remains future generic and
contract work; it adds no inheritance object, vtable, or runtime dispatch.

This model applies Zax's
[Expressiveness serves clear intent](principles.md#expressiveness-serves-clear-intent)
and
[Preference must be defensible, not guessed](principles.md#preference-must-be-defensible-not-guessed)
principles:

> Zax does not guess intent. A compatible source still needs one concrete
> destination and one unambiguous source region or mapping.

## Identity does not belong to shape

The identity of the compared outer type is outside its stored shape. Distinct
named and anonymous types can therefore have the same shape without becoming
the same type.

A transparent alias remains another name for one identity:

```zax
PixelPosition :: alias type PixelPoint
```

An inferred declaration also reuses the initializer's exact type rather than
creating another type:

```zax
original : :: type {
  x : Integer
  y : Integer
}

copy := original
OriginalType :: alias type type of original
another : OriginalType = original
```

`original`, `copy`, and `another` have the same anonymous type identity.
`OriginalType` only gives that identity a usable alias. Repeated evaluation of
one anonymous type-producing declaration or generated operation creates more
instances of its stable compile-time identity, not a new type for every value.

## Direct shape and flattened shape

### Direct stored shape

The direct stored shape records:

- direct resident-member names;
- declaration order;
- containment boundaries;
- complete nested type identities after transparent aliases normalize;
- type-side qualifications at every represented layer; and
- hidden structural components that affect representation.

Declaration order is observable beyond bytes: it also controls automatic
construction and reverse destruction.

Functions, `once` declarations, abstract roles, published paths, routes,
generated wrappers, and other no-instance-storage declarations are not resident
members. They may affect callable conformance or reflection without changing
stored shape.

### Flattened shape

Flattened shape removes eligible by-value containment boundaries and compares
the resulting ordered leaves.

```zax
Point2D :: type {
  x : Integer
  y : Integer
}

Point3D :: type {
  x : Integer
  y : Integer
  z : Integer
}

A :: type {
  coord : Point2D
  z : Integer
}

B :: type {
  coord : Point3D
}

C :: type {
  x : Integer
  y : Integer
  z : Integer
}
```

`A`, `B`, and `C` do not have the same direct shape:

- `A` directly contains `coord : Point2D` and `z`;
- `B` directly contains `coord : Point3D`; and
- `C` directly contains `x`, `y`, and `z`.

They may have the same flattened leaf shape and layout: three consecutive
`Integer` leaves named `x`, `y`, and `z`.

Flattening never descends through a pointer, reference, optional, union,
variant, tracking component, or another semantic-indirection boundary. Such a
value can participate as one atomic leaf when its complete type and
qualifications match.

A type with custom copy, ownership, construction, replacement, or destruction
behavior also remains atomic by default. Flattening through it could bypass the
operation that preserves its resources or invariants. A future type-owned bridge
may grant that stronger relationship deliberately.

### Unions and variants remain semantic leaves

An unmanaged union has one backing representation and several offset-zero
lenses. Those lenses are not direct stored members that structural flattening
may gather independently. Whole-union compatibility compares the complete union
identity and representation contract; explicit lens access selects a local
interpretation. Complete behavior belongs to [Zax unions](unions.md).

A managed variant has a wrapper and zero or one conditional payload path.
Structural conversion cannot choose an active name, manufacture absence, or
flatten every alternative into simultaneous members. The complete variant is
one leaf unless a future explicit variant-aware transformation defines another
relationship. See [Zax variants](variants.md).

A variant alternative is not an anchorable resident path:

```zax
choice anchor .text // error: text is a conditional alternative
```

A physical member whose complete value is a variant can be an anchor:

```zax
container anchor .choice
```

That selects the complete wrapper as one atomic leaf. It does not enter or
select an active payload.

## Compatibility posture

A **compatibility posture** says which safe structural relationship a source is
willing to offer when another concrete type is already expected.

It is deliberately distinct from transfer stance:

- `copy`, `deep`, `move`, and `last` describe resource transfer; and
- compatibility posture describes permission for low-overhead binary recasting.

Every declaration resolves one posture. Omission means:

```zax
compatible strict
```

The complete safe posture family is:

```zax
compatible strict
compatible shape
compatible flattened shape
compatible layout
compatible flattened layout
```

- `strict` permits only exact identity and transparent aliases to bind
  contextually.
- `shape` requires matching direct names, order, nested identity,
  qualifications, resident boundaries, and binary layout.
- `flattened shape` may remove eligible containment boundaries but still
  requires matching ordered leaf names, complete leaf types, qualifications,
  and binary layout.
- `layout` preserves direct resident boundaries and complete qualified member
  types while ignoring member names.
- `flattened layout` may remove eligible containment boundaries and ignore leaf
  names while retaining complete qualified leaf types and binary layout.

Every non-strict posture uses one contiguous region. It permits at most a fixed
offset adjustment and reinterpretation in addition to the ordinary construction,
assignment, or reference binding requested by the destination. It never gathers
scattered members or reorders values.

### Posture-bearing results

A result can deliberately offer structural flexibility to an API that expects
another identity:

```zax
RenderPoint :: type {
  x : I32
  y : I32
}

SensorPoint :: type {
  x : I32
  y : I32
}

drawPoint final : ()(
  point : RenderPoint readonly &
) = {
}

makeSensorPoint final : (
  result : SensorPoint copy compatible shape
)() = {
}

drawPoint(makeSensorPoint())
// RenderPoint is expected, so the result posture permits compatible
// construction of a RenderPoint temporary.
```

The function still returns exactly `SensorPoint`. Its visible result contract
permits compatible construction of the already requested `RenderPoint`.

Posture affects viability but does not invent overload preference. Exact
identity remains better than posture-based adaptation. If several non-identity
destinations remain possible, the use is ambiguous rather than being selected
by name count, offset, source order, or apparent closeness.

### Posture does not propagate accidentally

Compatibility posture belongs to the declaration or result contract, not to
canonical type identity.

```zax
captured := makeSensorPoint()
// captured has SensorPoint identity and compatible strict.

drawPoint(captured) // error: drawPoint requires RenderPoint
```

An inferred binding does not carry the result's implicit conversion authority
away from the API boundary. The programmer can restore it for one use:

```zax
drawPoint(captured as compatible shape)
// succeeds: this use again offers compatible shape
```

Or a declaration can retain the posture while inferring the base identity:

```zax
retained : compatible shape = makeSensorPoint()
drawPoint(retained)
// succeeds: retained explicitly offers compatible shape
```

An explicitly typed declaration, parameter, forwarding result, recomposed
value, or transformed value establishes its own posture. Anonymous values and
compiler-generated report types default to `compatible strict` unless their
owner explicitly says otherwise.

No coercive or unsafe posture can be declared, including on a function result.

### Type aliases may overlay posture

A concrete type alias may supply or replace an inherited safe compatibility
posture:

```zax
ShapeView :: alias type SensorPoint readonly & compatible shape
StrictView :: alias type ShapeView compatible strict
```

Both aliases retain `SensorPoint` canonical identity. `StrictView` replaces only
the posture axis and inherits the remaining profile before ordinary defaults
apply.

This does not recast a value or prove structural compatibility. An actual source
must still satisfy the resolved destination and posture requirements. Coercive
or unsafe relationships remain operations and cannot be smuggled into an alias
profile.

The general property-overlay order is defined by
[declarations and bindings](declarations-and-bindings.md#exact-aliases-and-property-overlays).

## Select one contiguous region with `anchor`

`anchor` is needed when the compatible bytes begin inside a larger value rather
than at its root:

```zax
TaggedPoint :: type {
  tag : Integer
  x : I32
  y : I32
}

RenderPoint :: type {
  x : I32
  y : I32
}

drawPoint final : ()(
  point : RenderPoint readonly &
) = {
}

tagged : TaggedPoint compatible shape

drawPoint(tagged) // error: complete TaggedPoint begins with tag
drawPoint(tagged.x) // error: tagged.x is only I32

drawPoint(tagged anchor .x)
// succeeds: compatibility starts at x, followed by y
```

The `drawPoint` prototype supplies the required `RenderPoint` destination. The
anchor path belongs to `tagged` and selects where that compatible region starts.
It is not a destination member name.

The compiler does not search for a matching region. Without an anchor, matching
starts at the source root and uses the complete source extent. With an anchor,
matching starts at the exact resolved place. A missing, inaccessible,
ambiguous, misaligned, or insufficient path is an error.

A larger value may contain several plausible regions. The programmer chooses
one rather than asking the compiler to guess:

```zax
LineCoordinates :: type {
  startX : I32
  startY : I32
  endX : I32
  endY : I32
}

line : LineCoordinates compatible layout

drawPoint(line) // error: LineCoordinates is not one RenderPoint
drawPoint(line.startX) // error: line.startX is only I32
drawPoint(line anchor .startX) // selects startX and startY
drawPoint(line anchor .endX)   // selects endX and endY
```

An anchor must identify resident storage. It may use:

- a physical stored path;
- an accessible `own`-published path; or
- a place-preserving data-`via` route.

The latter two canonicalize to their physical place before offset and layout
checks.

These declarations have no anchorable instance place:

```zax
perform final : ()() = {
}

shared once : Integer
required abstract : Integer
```

An attempt to anchor any of them is an error. Callable `via`, exposed behavior,
aliases without places, and metadata-only declarations are excluded for the
same reason.

### Default result anchors

A result can provide a source-relative default anchor:

```zax
PayloadSummary :: type {
  payloadSize : U32
  payloadChecksum : U32
}

Packet :: type {
  kind : Integer
  payloadSize : U32
  payloadChecksum : U32
}

consumePayloadSummary final : ()(
  payload : PayloadSummary readonly &
) = {
}

producePacket final : (
  result : Packet compatible shape anchor .payloadSize
)() = {
}

consumePayloadSummary(producePacket())
// PayloadSummary is expected; the result starts at .payloadSize and spans
// payloadSize plus payloadChecksum.
```

The producer knows `.payloadSize` inside its own result. It does not need to
know the caller's destination type. A caller can state another source anchor
when the result posture permits it.

Posture and default anchor are visible result-contract metadata, not part of
`Packet` identity.

### Anchoring one result among several

A bare multiple-result sequence is not one structural value and cannot be
anchored collectively. Select one result slot, then anchor within that result:

```zax
consumePointAndStatus final : ()(
  point : RenderPoint readonly &,
  status : Boolean
) = {
}

produceLine final : (
  line : LineCoordinates compatible layout,
  status : Boolean
)() = {
}

consumePointAndStatus(
  line anchor .startX: point:,
  status: = produceLine()
)
```

The routing pattern is:

```text
<source-result label> anchor <source-path>: <destination>:
```

One result slot can be consumed only once. Two anchors do not split one result
implicitly. Use explicit decomposition, copying, or another producer invocation
when two destinations require it.

An anchored by-value result temporary survives through its complete synchronous
consumer. A reference into it cannot escape. Anchoring a reference result
preserves the original referent origin and lifetime.

### Zero-size places

A stored zero-size value remains a conceptual place with its own path, origin,
and lifetime:

```zax
Marker :: type {
}

Container :: type {
  first : Marker
  second : Marker
  value : Integer
}
```

`first` and `second` remain distinct even if they share a machine address. A
zero-size anchor may supply only a compatible zero-size destination. It cannot
use address coincidence to consume `value` or another following member.

## Protected compatibility conversions

The safe protected source patterns are:

```text
<source> as shape <DestinationType>
<source> as flattened shape <DestinationType>
<source> as layout <DestinationType>
<source> as flattened layout <DestinationType>
```

Each may carry a source anchor:

```text
<source> as shape <DestinationType> anchor <source-path>
<source> as flattened shape <DestinationType> anchor <source-path>
<source> as layout <DestinationType> anchor <source-path>
<source> as flattened layout <DestinationType> anchor <source-path>
```

These forms cannot be overloaded. Ordinary:

```text
<source> as <DestinationType>
```

continues to select applicable type-owned conversion. It does not silently
request structural compatibility.

### Value, assignment, and reference destinations

One compatibility relation can participate in three destination contexts:

```zax
kilometers : KilometerPoint

copy : PixelPoint =
  kilometers as shape PixelPoint

existing : PixelPoint
existing =
  kilometers as shape PixelPoint

view : PixelPoint & =
  kilometers as shape PixelPoint &
```

- A by-value destination runs applicable destination construction using the
  compatible source view.
- An existing destination runs applicable destination assignment.
- A reference destination creates a same-storage view and by itself runs
  neither construction nor assignment.

Compatibility adds no arbitrary reshaping cost. Destination construction,
assignment, allocation, and transfer retain their ordinary visible costs.

## Same-storage compatible references

A compatible reference provides another typed path to the same resident
storage:

```zax
PixelPoint :: type {
  x : Integer
  y : Integer
}

Coordinates :: type {
  horizontal : Integer
  vertical : Integer
}

point : PixelPoint

coordinates final :
  Coordinates mutable writable final & =
    point as layout Coordinates &

coordinates.horizontal = 20
// point.x is now 20.
```

Mutation through a writable view intentionally mutates the source storage.
Every participating leaf still preserves its actual type-side qualifications.
Safe compatibility never presents immutable state as mutable, readonly access as
writable, a final place as varying, or a nested pointer/reference with stronger
authority than its source.

Creating the view begins no separate destination value lifetime. Direct
destination construction or destruction through the view is unavailable.
Ordinary destination functions and operators can act through it when their
qualified in-lifetime behavior preserves a valid source representation.

### Anchored regions are final subplaces

An interior region does not inherit the containing declaration's whole-value
replacement authority:

```zax
a : A mutable

c final :
  C mutable writable final & =
    a as flattened layout C & anchor .coord.x

c.y = 10      // content mutation is permitted
c .= makeC()  // error: this subregion is not independently varying
```

If `a` is immutable, deep immutability applies to its composed values:

```zax
a varying : A immutable writable varying

c final :
  C immutable readonly final & =
    a as flattened layout C & anchor .coord.x
```

The outer `A` place can undergo complete `.=` reconstructive replacement through
`a`, but the interior view cannot gain mutable or varying authority.
Reconstructing `a` invalidates `c`; it does not make `c` observe a renewed
interior.

### Whole-root varying views require full compatibility

A view without an anchor may retain the complete outer place's
`immutable writable varying &` capability only when compatibility proves:

- equal complete extent and alignment;
- corresponding resident-lifetime partitions;
- equal complete qualifications;
- compatible tracking components; and
- compatible construction, replacement, and destruction obligations.

Two simple structurally generated record types can satisfy those conditions and
permit reconstructive replacement through the compatible root view. Equal bytes
alone are insufficient. Incompatible custom replacement, destruction, or
source-only resource obligations make the varying reference conversion an
error unless a future explicit bridge or local unsafe operation accepts the
stronger responsibility.

## Transfer stance and anchored regions

A whole-source compatible recast preserves its source transfer stance.

An anchored region receives its own default `copy`, like a new reference alias.
The containing source's `last` does not prove that terminally consuming an
arbitrary interior region leaves excluded state destruction-valid.

```zax
(source anchor .payload) as last
```

This explicit request is viable only when the applicable source contract proves
that the complete source remains destruction-valid. A result can establish that
contract for one default anchor:

```zax
result :
  Container last
  compatible layout
  anchor .payload
```

A caller-selected different anchor does not inherit the guarantee.

If conversion selects `copy`, the region receives `copy` even when the outer
declaration ordinarily offers `last`.

## `outer tracked` participates in structure

`outer tracked` contributes a hidden root structural component with placement
and lifecycle meaning. The language does not promise that it is a pointer or
one particular metadata block.

Safe compatibility requires:

- a tracked destination to find a compatible tracking component at its relative
  root;
- aligned tracking components with the same metadata contract to compare as
  compatible even when their outer identities differ;
- a tracking component never to match ordinary data merely because byte size
  agrees; and
- flattening never to enter the component's internal representation.

A same-storage compatible reference shares the source resident's tracking state.
A destination copy instead repairs or regenerates tracking for its new
placement.

An anchor can begin at the first visible member after an outer tracking
component when the destination is untracked. If an anchored region contains a
nested tracking component, the destination must expect a compatible component
at the same relative position.

Recasting does not make an incorrect outer relationship succeed. Tracked
operations still test their requested physical placement normally.

## Coercive conversion and raw casting

### Coercive reference views

Coercion gives the same stored bits another recognized lens when that lens
provides more insight than raw byte access. For example, viewing a Binary32
value through same-endian U32 exposes known sign, exponent, and fraction
positions instead of only an unexplained byte sequence.

Precisely, coercion verifies a contiguous target representation and performs
zero translation while accepting a reviewed semantic reinterpretation. Changing
numeric value is not inherently unsafe. Its purpose becomes clearer for a
complete aggregate than for one scalar that could be viewed independently:

```zax
SignedRegisterBlock :: type {
  control : I32
  mask : I32
}

RawRegisterBlock :: type {
  control : U32
  mask : U32
}

writeRawRegisters final : ()(
  registers : RawRegisterBlock readonly &
) = {
  // Write both words exactly as represented.
}

signedRegisters : SignedRegisterBlock
signedRegisters.control = -1
signedRegisters.mask = -2147483648

writeRawRegisters(signedRegisters) // error: requires RawRegisterBlock

writeRawRegisters(
  signedRegisters as layout RawRegisterBlock &
) // error: safe compatibility requires matching scalar formats

writeRawRegisters(
  signedRegisters as coercive layout RawRegisterBlock &
)
// succeeds: reinterpret both equal-width words without numeric conversion
```

The consumer needs one `RawRegisterBlock`, not two unrelated integers.
Per-member `as U32` would perform numeric conversion and may reject negative
values. Coercive layout instead preserves the complete contiguous bit pattern
and creates a same-storage readonly view without gathering or converting
members.

The representation checks remain:

```zax
WideRegisterBlock :: type {
  control : I64
  mask : I64
}

wide : WideRegisterBlock

writeRawRegisters(
  wide as coercive layout RawRegisterBlock &
) // error: corresponding integer leaves have unequal logical widths
```

Coercion produces only a reference view:

```text
<source> as coercive layout <DestinationType> &
<source> unsafe as coercive layout <DestinationType> &
```

There is no bare by-value coercive result. Representation extraction/adoption or
explicit destination construction owns any independent copy.

Safe coercion requires:

- a reviewed scalar or aggregate relationship that provides the useful second
  interpretation;
- every valid source representation to be valid in the destination;
- any requested writable access to preserve validity of the original
  representation;
- sufficient extent and alignment; and
- preserved qualification and lifetime authority.

`unsafe as coercive` is required when initial destination validity is asserted
rather than proved or when writes through the view can make the original scalar
representation invalid. The programmer must restore that original validity
before using the original type again.

Both forms are target-dependent and always local; no declaration or result can
carry a coercive posture. Neither grants access authority. Existing mutable,
writable, or varying authority may be preserved when ordinary lifetime and
whole-root conditions permit it; immutable, readonly, or final input cannot
become stronger. Coercion cannot bypass bounds, lifetime, resource,
construction, replacement, or destruction invariants.

### View-shaped `unsafe cast`

When no compatibility posture, anchor, or coercion can establish a
relationship, `unsafe cast` remains available as unchecked reinterpretation
outside the compatibility family:

```zax
view : Destination & = value unsafe cast Destination &
```

It verifies no shape, layout, posture, anchor, qualification, invariant,
lifetime, bounds, or target-layout guarantee, and it produces only views. To
obtain a new structural value, form the view and construct the destination from
it. Prefer the protected compatibility and coercion forms whenever they apply:
they state and check the relationship that `unsafe cast` leaves to the
programmer.

Complete `unsafe cast` behavior, including by-value construction, vacancy,
qualification, and pointer roles, is owned by
[Zax conversions and casts](casting.md#unchecked-reinterpretation-with-unsafe-cast).

## Scalar compatibility

Every scalar family supplies semantic format properties used by safe
compatibility and a separately reviewed reduced list used by coercion.

### Integer and fixed-point scalars

The complete safe integer/fixed-point format includes:

- coefficient logical width;
- signedness and value encoding, including two's-complement representation;
- fixed fractional-bit position, including integer `F = 0`;
- concrete endianness and logical-bit placement; and
- storage extent, alignment, non-value bits, valid patterns, and normalization
  rules.

Every applicable property must match safely. The complete family facts belong
to [integers](integers.md#structural-scalar-compatibility) and
[fixed-point scalars](fixed-point-scalars.md#representation-and-coercion).

Integer/fixed-point coercion requires:

- equal logical coefficient width;
- equal selected storage extent;
- source placement satisfying destination alignment;
- logical-bit placement appropriate to the declared relation; and
- destination-compatible padding and validity.

It may reinterpret signedness or `F`. Cross-endian coercion may expose the
precisely known byte-order reinterpretation. Numeric conversion remains
separate.

### Floating-point scalars

Safe floating compatibility requires the same complete format: logical width,
sign encoding, exponent allocation and bias, fraction/significand
interpretation, implicit/explicit integer-bit mode, value classes,
payload/canonicalization rules, endianness, field placement, extent, alignment,
and non-value-bit behavior.

Two different floating formats do not become coercion-compatible merely because
their extent matches. Repartitioning exponent and fraction fields provides no
reviewed representation lens.

A float may instead coerce directionally to an equal-width, same-endian unsigned
integer when its logical fields, extent, alignment, and padding correspond. The
integer may safely coerce back when every source pattern is a valid destination
encoding. Thus Binary32 and matching U32 are safely bidirectional, including
writable views.

X87Extended80 can safely produce a readonly U80 view. A writable U80 view is
unsafe because a write may invalidate the original Extended80 value. U80 to
X87Extended80 requires validation or an unsafe validity assertion because some
U80 patterns are not Extended80 values.

Complete format and representation-adoption behavior belongs to
[floating-point scalars](floating-point-scalars.md#structural-compatibility-and-coercion).

### Coercion is more than raw bits

The selected coercive relation identifies how destination fields or coefficient
positions correspond to the same stored bits. Numeric value may change, but the
programmer gains a type-aware interpretation rather than losing all meaning.

When no family-reviewed relation supplies that additional information, use
view-shaped `unsafe cast`. Equal byte count alone is never sufficient for
coercion.

### Enums are directional

An enum may be a compatibility source only for its compatible immediate
underlying scalar type. The underlying scalar does not automatically become the
enum, and one enum cannot safely recast into another enum merely because their
backing formats match.

Coercive enum conversion is directional. It may target a relaxed or other enum
whose ordinary admission accepts every value of its compatible backing
representation. A strict, flags, or otherwise restricted destination remains
ineligible; the fact that `unsafe from` could bypass admission does not make its
ordinary domain open.

Concrete endian specializations are intrinsic scalars rather than semantic
enums. Numeric endian conversion, representation extraction/adoption, and
byte-preserving coercion remain explicit operations owned by
[endianness](endianness.md).

## Packets construct; they are not anonymous values

A construction packet can use an already selected concrete parameter type as
its destination:

```zax
draw final : ()(point : PixelPoint &) = {
}

draw(point: [{ .x = 4, .y = 5 }])
draw([{ .x = 4, .y = 5 }]) // positional form
```

This constructs an actual `PixelPoint`. It does not create an anonymous
structure or use compatibility.

The explicit typed named form remains available:

```zax
draw(point: (: PixelPoint = [{ .x = 4, .y = 5 }]))
```

`point:` is the named parameter mapping. `(: PixelPoint = ...)` is an anonymous
typed declaration expression; neither role is implicit in the other.

A packet still is not an independently typed expression value. Candidate
selection must find one unambiguous expected parameter type before the packet
has a destination.

## Opening and packing structural values

The three structural operators have a visual mnemonic:

- `>-`: the right-hand `-` is a packed source; the open side of `>` releases
  named members toward destinations on the left.
- `-<`: open results on the right flow into `<`, which closes them into the
  packed destination represented by the left-hand `-`.
- `-<>-`: the right packed source opens through `>-`; those values then close
  through `-<` into the left packed destination.

The punctuation shows why these operations are related without pretending they
have the same cardinality or cost.

### Decompose one value with `>-`

`>-` makes eligible named source members available to a mapping-capable
destination:

```zax
Point :: type {
  x : Integer
  y : Integer
  description : String
}

plot final : ()(
  x : Integer,
  y : Integer,
  label : String = "point"
) = {
}

point : Point
plot(>- point)
```

`x` and `y` map by name. `label` uses its default. `description` remains an
untouched member of `point`.

There is no automatic positional fallback. A candidate must obtain one complete
deterministic mapping from names, explicit arguments, and declared defaults.
“Most names matched” and “longest match” are not overload preferences.

Decomposition exists to spread a value into call inputs. Into existing
destinations, it assigns. It has no construction form, so construct members
from a value's parts with one `.=` each:

```zax
+++ final : ()(point : Point) = {
  _.x .= point.x        // one operation per member: first construction
  _.y .= point.y
}

+++ final : ()(point : Point) = {
  x: _.x, y: _.y >- point
  // Two operations per member: automatic default construction, then assignment.
  // Valid only when the member types can be default-constructed.
}
```

An inaccessible member is ineligible. A pointer, reference, optional, or other
semantic wrapper remains one atomic source member rather than being entered
automatically.

### Recompose several results with `-<`

A callable's result slots remain several values until `-<` explicitly creates
one:

```zax
produce final : (
  count : Integer,
  text : String,
  valid : Boolean
)() = {
}

count:, remaining : -< produce()

print(remaining.text)
print(remaining.valid)
```

The routed `count` result is consumed first. The remaining labelled results
become direct members in producer result order. The resulting anonymous value
has one stable identity and defaults to `compatible strict`.

A named destination uses matching result names and ordinary destination
construction:

```zax
summary : Summary -< produce()
```

Unmapped destination members may use established construction defaults.
Non-discardable source results must be routed or recomposed; discardable results
may remain unused.

Named recomposition targets physical construction shape. It does not initialize
through `own` or data-`via` paths as though those paths were direct members.

### Transform one value with `-<>-`

`-<>-` performs real member mapping when a contiguous binary recast is
insufficient.

It can rename, reorder, gather noncontiguous members, change nesting, construct
a new destination, or assign an existing destination.

For example, a destination may carry state absent from the source:

```zax
Coordinates :: type {
  x : I32
  y : I32
}

Record :: type {
  x : I32
  y : I32
  label : String = "default"
}

coordinates : Coordinates
record : Record

updated : Record & =
  record -<>- coordinates
// record.x and record.y update; record.label remains unchanged.
```

When both operands are supplied, the complete expression updates the existing
left destination, leaves unmapped destination members unchanged, and returns a
reference without increasing authority. Unmapped source members remain
untouched.

When a declaration, assignment, call parameter, or result supplies a destination
hole, leading `-<>-` instead constructs one complete transformed destination:

```zax
created : Record =
  -<>- coordinates
// created.label is "default".

record =
  -<>- coordinates
// A complete transformed Record is constructed, then assigned to record.

immutableRecord varying :
  Record immutable writable varying

immutableRecord .=
  -<>- coordinates
// Construct a complete transformed Record, then reconstruct the varying place.
```

Both destination-hole forms construct a complete transformed `Record`.
Ordinary `=` then selects in-lifetime assignment; protected `.=` explicitly
selects reconstructive replacement. Every required member must be mapped or
established by an ordinary destination default; old unmapped destination
members are not retained.

Panic never rolls transformation back or returns partial state. A matching
helper repairs the blocked operation and lets it resume; otherwise the process
crashes gracefully.

## Reusable mapping with `reshape`

A reshape is a no-storage directional mapping declaration. It is useful when a
consumer requires a different name or order rather than a binary-compatible
view:

```zax
DevicePoint :: type {
  deviceY : I32
  deviceX : I32
}

RenderPoint :: type {
  x : I32
  y : I32
}

DeviceToRender :: reshape {
  deviceX: x:
  deviceY: y:
}

drawPoint final : ()(
  point : RenderPoint readonly &
) = {
}

devicePoint : DevicePoint

drawPoint(devicePoint) // error: DevicePoint members need remapping

renderPoint : RenderPoint =
  -<>- DeviceToRender -<>- devicePoint

drawPoint(renderPoint)
```

Every entry reads:

```text
<source path>: <destination path>:
```

Nested paths are legal:

```zax
MyFlattening :: reshape {
  coord.x: x:
  coord.y: y:
  z: z:
}
```

Explicit mappings consume both endpoints before automatic same-name matching
considers the remaining paths. This permits swaps:

```zax
SwapCoordinates :: reshape {
  x: y:
  y: x:
}
```

The explicit mappings prevent automatic `x: x:` and `y: y:` mappings.

A reshape has no instance, runtime identity, storage, constructor, or anchor.
It is checked when applied to concrete source and destination types. Reversing
the direction requires another declaration; invertibility is never guessed.

An exact alias adds another name without copying or reinterpreting the mapping:

```zax
RenderMapping :: alias reshape DeviceToRender
```

A forward may establish its source-ordered category before the mapping entries
arrive:

```zax
RenderMapping :: forward reshape
```

No transformation can execute until the direct reshape or exact alias
completion supplies the complete directional paths. General alias and forward
rules are defined by
[declarations and bindings](declarations-and-bindings.md#forward-anchors).

### Mapping callable results to inputs

A reshape can map one concrete callable result shape into another callable's
input shape:

```zax
ParseToRender :: reshape {
  tree: document:
  notes: diagnostics:
}

pipeline := parse >> ParseToRender >> render
```

The reshape remains no-storage. The complete three-part expression constructs
one composition and creates no intermediate structural value.

Mapping applies explicit entries first, then exact equal labels, then remaining
positions in declaration order. Destination defaults and source-result omission
apply afterward. Mapping never backtracks, and an incompatible equal-label pair
is an error until an explicit entry states another route.

Complete callable construction and invocation are defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#remap-with-reshape).

### Outcome reshape

A callable-surface reshape may rename ordinary success results and exceptional
outcomes without invoking, handling, or forwarding:

```zax
FailureNames :: reshape {
  value: producedValue:
  failure: parseFailure:
}

renamedParse := parse reshape FailureNames
```

One inline mapping can be local to one invocation:

```zax
parse(source) reshape failure: parseFailure:
```

Several mappings use one named or local anonymous reshape declaration:

```zax
parse(source) reshape {
  tree: parsedTree:
  notes: parseNotes:
  failure: parseFailure:
}
```

Every entry remains source-to-destination. Reshape entries are separate
declarations and never use commas. One application accepts either one inline
mapping or one reshape declaration; it does not accept several inline pairs.

Callable-surface reshape:

- preserves the exceptional category;
- keeps ordinary mappings in the success shape;
- preserves payload type, qualifications, stance, origin, and constructedness;
- creates no runtime value, storage, constructor, or control transfer;
- cannot map an ordinary result to an exceptional outcome or the reverse; and
- rejects duplicate destination labels.

Explicit entries apply before equal-label outcomes remain exposed. Applying a
named reshape to a callable adapts its visible outcome labels without invoking
it. This lets a retained composition establish unique outcome names before
`>>` combines stage contracts.

An invocation-level reshape affects only the selected invocation's exposed
outcome surface. Complete branch-polymorphic catch and forwarding behavior is
defined by [Zax exceptional result flow](except.md#nested-calls-expose-their-outcomes).

An existing destination can be updated directly through the same declaration:

```zax
renderPoint -<>- DeviceToRender -<>- devicePoint
```

The full left and right operands make this an expression whose result is a
reference to `renderPoint`. A contextual form instead uses `=` and leading
`-<>-` to construct a complete destination before assignment:

```zax
renderPoint =
  -<>- DeviceToRender -<>- devicePoint
```

Direct update keeps unmapped destination members. Constructed replacement uses
ordinary defaults for them.

The contextual form also works with `.=`, which constructs into an existing
place instead of assigning:

```zax
+++ final : ()(devicePoint : DevicePoint) = {
  _.renderPoint .= -<>- DeviceToRender -<>- devicePoint
  // The member starts empty: first construction, with elision possible.
}
```

On a live place, the same `.=` form is replacement. Direct update requires a
live destination, so it cannot construct an empty member or result slot. See
[Zax construction, replacement, and destruction](construction-and-destruction.md#construct-into-an-existing-place-with-dot-equals).

### Overlap is handled statically

The semantics are as if every mapped by-value source needed before an
overlapping write were captured first.

For a known swap, the mapping graph requires an intermediate. For proven
disjoint source and destination storage, no intermediate is needed. When
references or aliases make overlap possible but do not establish the exact
relation, transformation uses a complete source temporary unconditionally.
It does not emit a runtime address-range test.

If no applicable copy, move, or other capture can establish the temporary, the
transform is unavailable unless future analysis-control work supplies an
explicit non-overlap contract.

A direct same-member mapping needs no intermediate, but its selected assignment
still executes when that operation has observable behavior.

## Transforming several results

Transformation belongs to each selected result, not to the complete producer
sequence. Result routing preserves its established source-to-destination order
and inserts transformation between those two roles:

```text
<source-result label>: -<>- [<reshape> -<>-] <destination>
```

For example:

```zax
RenderSize :: type {
  width : I32
  height : I32
}

DeviceSize :: type {
  deviceWidth : I32
  deviceHeight : I32
}

DeviceToRenderSize :: reshape {
  deviceWidth: width:
  deviceHeight: height:
}

consumeParts final : ()(
  point : RenderPoint,
  size : RenderSize,
  status : Boolean
) = {
}

produceParts final : (
  oldPoint : DevicePoint,
  oldSize : DeviceSize,
  status : Boolean
)() = {
}

consumeParts(
  oldPoint: -<>- DeviceToRender -<>- point:,
  oldSize: -<>- DeviceToRenderSize -<>- size:,
  status: = produceParts()
)
```

The final `= produceParts()` remains the ordinary one-producer routing boundary.
`status` maps directly; only entries containing `-<>-` transform.

An explicit leading bare `:` selects the current positional source-result cursor
before naming or declaring the destination:

```zax
: -<>- DeviceToRender -<>- myPoint : RenderPoint,
: -<>- DeviceToRenderSize -<>- mySize : RenderSize,
status: = produceParts()
```

The first side is always the selected source result and the final side is its
destination, matching ordinary `<source>: <destination>:` routing. Structural
value transformation has no use-site `reshape` keyword; the contextual
use-site keyword is reserved for callable outcome relabeling described above.

The group never uses `= -<>- produceParts()`: that would incorrectly imply that
every result transforms. The transformed entries also do not combine several
results into one structural value; use `-<` for that.

## Composition paths can map without becoming shape

An accessible `own`-published path or place-preserving data-`via` route may be
used as:

- a source path for `>-`, `reshape`, `-<>-`, or `anchor`;
- a destination path for explicit reshape and existing-destination
  transformation; or
- a construction destination designator when transformation can establish the
  complete underlying physical member and every enclosing lifetime.

`preferred`, callable `via`, `expose`, abstract roles themselves, and generated
callable surfaces do not provide resident data paths.

Automatic same-name transformation uses ordinary composition lookup:

```zax
Engine :: type {
  rpm : Integer
}

Car :: type {
  engine own : Engine
}

Snapshot :: type {
  rpm : Integer
}

snapshot : Snapshot -<>- car
```

`car.rpm` resolves to `car.engine.rpm`. Every alternate path canonicalizes to
the physical resident place before consumption is tracked. Two aliases cannot
make that one place satisfy two automatic destinations.

For new construction, a route only designates its physical target.
Transformation does not default-construct the containing value and assign
through the route afterward.

Name-aware compatibility continues to use physical declaration names. A renamed
`via` can help transformation but cannot manufacture shape equality.

## Arrays remain structural leaves

Structural conversion treats a complete array as one member. It does not unpack
every element merely because it is comparing flattened shapes:

```zax
MyRecord :: type {
  values : Integer[100]
}
```

`values` remains one `Integer[100]` member; it does not become one hundred
implicit `Integer` members.

Comparing two owning array leaves considers their element type, dimensions,
size limits, qualifications, and resolved storage profile. Storage does not
change logical array identity, but inline versus provider-backed representation
can change the complete stored shape and `size of`.

A storage-erased array reference provides ordinary array operations without
claiming that the several owning representations have one binary layout. A
slice remains one borrowed view rather than a flattened list of its current
elements.

Changing an array of structures into a structure of arrays rearranges how data
is grouped. That can require visiting elements, allocating storage, constructing
destinations, transferring values, and handling overlap. The programmer must
request an explicit transformation; ordinary `as shape` or `as layout` does not
perform it.

Complete array dimensions, layout guarantees, element lifetimes, and future
AoS/SoA pressure are explained by
[Zax arrays and slices](arrays-and-slices.md#structural-and-reflection-boundaries).

## Shared hidden arithmetic reports

Integer report types are anonymous to their caller-facing source surface, but
they are stable compiler-owned identities.

Conceptually, one concrete report family can use:

```zax
I32WrappedOverflowReport :: type {
  value : I32
  overflowed : Boolean
}
```

Relevant `I32` addition, subtraction, and multiplication report operations may
all return that one hidden identity. `U64` receives another identity because
its contained scalar differs. Combined, saturated, and transition reports use
separate shared identities when their schemas differ.

The generated identity owns `?report` and `!report`. A programmer-defined value
does not acquire those operations merely by matching its members. A caller may
use `type of` to create a visible alias to the hidden identity.

## Costs

Compatibility and transformation are not one cost class:

- a same-storage compatible reference adds no destination value or independent
  destruction;
- an anchor may require one fixed address adjustment;
- by-value compatibility still runs ordinary destination construction and
  transfer;
- `copy`, `deep`, `move`, and `last` retain their type-specific costs;
- transformation may evaluate member operations, construct nested values, and
  require temporaries for overlap;
- `outer tracked` permits hidden representation and maintenance work;
- coercive conversion and `unsafe cast` add audit and portability cost even when
  runtime work is small; and
- posture-enabled candidates and structural mappings add compile-time analysis.

An implementation may inline wrappers, elide temporaries, combine copies, or use
raw machine operations only when the programmer-visible construction, mutation,
lifetime, ordering, and failure behavior remains unchanged.

## Diagnostics

Diagnostics should distinguish:

- exact identity mismatch from structural incompatibility;
- direct shape, flattened shape, layout, and flattened-layout failure;
- safe incompatibility from coercive target-layout failure;
- mismatched scalar format property;
- insufficient size, alignment, extent, or hidden tracking component;
- an anchor that is missing, inaccessible, ambiguous, non-storage, zero-size
  for a nonzero destination, or outside the source extent;
- qualification weakening;
- unavailable destination construction or assignment;
- a same-storage view requesting unavailable replacement authority;
- positional decomposition intent where only name mapping exists;
- missing, duplicate, inaccessible, or multiply consumed reshape paths;
- outcome reshape whose source label is absent, destination label collides, or
  mapping crosses the ordinary/exceptional category boundary;
- comma-separated reshape entries or several inline mappings where one
  declaration is required;
- required results left unconsumed;
- overlap capture unavailable for the required transfer;
- pointer-copy lifetime proof deferred to another owner;
- attempted coercive posture declaration; and
- by-value `unsafe cast`.

Diagnostics lead with programmer names and source paths. Expanded output may
show canonical physical paths, member offsets, scalar-format properties,
tracking components, and selected transfer operations.

## Source stability

These changes can alter source validity or behavior:

- renaming or reordering a stored member;
- renaming an exceptional outcome used by catch, forwarding, or retained
  composition;
- adding or removing a direct or flattened leaf;
- changing member identity or qualification;
- changing scalar width, sign, encoding, fractional position, or endian role;
- changing size, alignment, padding, or tracking capability;
- adding or removing a compatibility posture or default result anchor;
- changing an `own` or data-`via` path used by mapping;
- changing a reshape entry or making two paths reach one place;
- changing destination construction, assignment, copy, replacement, or
  destruction behavior;
- adding a source-family result or changing a result label used by
  recomposition;
- changing a hidden report schema; and
- changing a target so a safe or unsafe coercive layout no longer matches.

No ambiguity is resolved by declaration, import, source, or physical offset
order.

## Boundaries and future pressure

This document is current conceptual design, not a formal grammar, ABI contract,
reflection API, generic constraint system, pointer-proof system, or
implementation mapping.

Future focused work owns:

- whole-type no-storage contracts and generic structural constraints;
- reflection of posture, anchors, shape, physical/alternate paths, tracking,
  scalar formats, reshape declarations, and hidden report identities;
- future scalar families supplying their own safe/coercive leaf relationships;
- pointer-copy provenance when slicing separates a pointer from referenced
  storage;
- reflection of how storage added by [partials](partials.md) changed a
  completed shape;
- explicit non-overlap analysis contracts;
- ABI/FFI layout and calling-convention guarantees;
- general casting outside the protected forms integrated here; and
- formal recursive comparison, parser grammar, compiler canonicalization,
  lowering, and optimization algorithms.

Those reviews must preserve strict identity by default, contiguous posture-based
recasting, explicit shape-changing operations, exact qualification, and loud
ambiguity.
