# Zax enums

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers declaring, reading, converting, or operating on enum values |
| Applies To | Programmer-facing strict, relaxed, and flags enum behavior; not a formal grammar, ABI contract, or specification |
| Implementation State | Not established by this repository |
| Owns | The enum mental model; enum-category forwarding; declaration policies; backing eligibility; members, aliases, defaults, and bodies; safe admission and reachable unnamed values; generated comparison and underlying operations; selective operation reuse; flags masks and operations; generated string conversion; enum declaration-traversal facts and local use; enum-domain selection and coverage facts; enum costs, diagnostics, and source stability |
| Does Not Own | General identity mechanics ([identity types](identity-types.md)); integer representations and families ([integers](integers.md)); [structural shape and compatibility](structural-shapes-and-compatibility.md); shared operator selection ([operators](operators.md)); exact operator forms ([operator catalog](operator-catalog.md)); general safety categories ([safety and analysis](safety-and-analysis.md)); complete iteration, runtime switch behavior ([switch, case, and default](switch.md)), pattern matching, reflection, generics, general partial behavior ([partials](partials.md)), ABI, or FFI |
| Source / Provenance | Current identity, integer, declaration, operator, safety, and intent designs, incorporating reviewed legacy enum intent |

## Start with known values

An enum presents an integer-backed value as a distinct type and may give useful
values names:

```zax
Color :: enum U8 {
  Red
  Green
  Blue
}

color : Color = Color.Red
```

`Color.Red` is a `Color`, not an uncommitted integer or a special
member-specific type. The integer backing does not make a `Color`
interchangeable with `U8`:

```zax
raw : U8 = 1

color = raw // error: no implicit crossing of the enum identity
```

An enum is a specialized
[identity](identity-types.md#identity-boundary-and-underlying-type). It has one
immediate integer backing value, adds no stored per-value state, and controls
which backing operations become available through the enum identity.

### Forward an enum category

An enum name may be anchored before its backing and members complete:

```zax
Color :: forward enum

favorite : Color

Color :: enum U8 {
  Red
  Green
  Blue
}
```

`forward enum` preserves the enum category rather than treating it as an
unspecialized `forward type`. Member suffixes remain pending under the forwarded
root, so there is no separate `forward enum value` form. Uses requiring backing,
members, layout, admission, or generated operations wait for completion.
General matching and completion rules are defined by
[declarations and bindings](declarations-and-bindings.md#forward-anchors).

## Three declaration policies

Zax provides three enum policies:

```zax
StrictEnum :: enum U8 {
  First
  Second
}

RelaxedEnum :: enum relaxed U8 {
  Zero
  Ninety = 90
}

Permission :: enum flags U8 {
  Read = 1
  Write = 2
  Execute = 4
}
```

- A plain enum is **strict**. Ordinary safe admission from its backing accepts
  named member values.
- A `relaxed` enum safely admits every backing value and exposes the eligible
  operation surface of its backing identity.
- A `flags` enum admits every combination of the bits named by its members and
  supplies a closed set of bitwise operations.

`relaxed` and `flags` are mutually exclusive. Strict is the ordinary policy and
needs no keyword.

These policies control ordinary admission and generated behavior. They do not
change the fact that every backing representation can be held as a defined enum
value after an authorized enum operation or explicit unsafe adoption produces
it.

## Members

An ordinary or relaxed member has this shape:

```text
UpperName [= integer constant expression] [default]
```

Member declarations do not use commas:

```zax
Status :: enum I8 {
  Ready
  Waiting = 4
  Paused = (
    2 + 3
  ) default
}
```

A member name and a following `=` or `default` remain in one effective
statement. The value expression follows ordinary
[expression-continuation rules](source-structure.md). `default` follows the
member name or its completed value expression with separating whitespace.

Every member value must resolve while the enum definition is completed to an
integer constant representable by the backing type. Broader compile-time
execution may eventually expand how such a constant is produced; it does not
permit a runtime-dependent member.

### Implicit values

For strict and relaxed enums, the first implicit value is zero. Each later
implicit value is the preceding member's value plus one, including after an
explicit value:

```zax
Sequence :: enum I8 {
  First       // 0
  Tenth = 10
  Eleventh    // 11
}
```

Implicit progression that exceeds the backing range is an error. It never
wraps.

Flags do not use implicit progression. Every flags member states its value:

```zax
Permission :: enum flags U8 {
  Read = 1
  Write = 2
  Execute = 4
  ReadWrite = 3
}
```

Sequential `+ 1` values would obscure which bit or composite the declaration
intends.

### Aliases

Member names are unique, but several names may have the same backing value:

```zax
Fruit :: enum U8 {
  Apple = 4
  Grapefruit = 4
}

same := Fruit.Apple == Fruit.Grapefruit // true
order := Fruit.Apple <=> Fruit.Grapefruit // equal
```

The runtime value does not remember which member name produced it. Declaration
order still matters for generated string conversion and future member
iteration.

All enum members are public. An enum cannot declare a private member value.

## Defaults

For a strict or relaxed enum:

- one member may carry `default`;
- the marker selects that member;
- otherwise the first member is the default; and
- an enum with no members uses the backing type's default value.

That enum default also supplies the compiler-prepared representation when an
enclosing Nothing instance needs a readable enum member. This is preparation of
special backing, not ordinary construction of the enclosing type. Trapping and
custom policies remain available through
[Zax Nothing instances](nothing-instances.md).

```zax
Fruit :: enum I8 {
  Apple = 4
  Orange = 42 default
}

fruit : Fruit // Fruit.Orange
```

For a flags enum, absence of a marker defaults to zero, even when no member
names zero. One member may instead be selected:

```zax
Permission :: enum flags U8 {
  None = 0
  Read = 1
  Write = 2
  Typical = 3 default
}

permission : Permission // Permission.Typical
```

The selected flags default may be nonzero or composite. Zero remains safely
admissible.

An empty strict enum demonstrates that default construction and backing
admission are different operations:

```zax
Empty :: enum U8 {
}

value : Empty // defined unnamed value backed by zero

raw : U8 = 0
maybe := Empty optional from raw // absent: Empty has no named members
```

## The member prologue and enum body

An enum body begins in member mode. The first complete statement that does not
have member shape begins ordinary definition mode:

```zax
Color :: enum {
  Red
  Green
  Blue

  MySubType :: type {
  }

  log final : ()() readonly = {
    // `_` is the Color receiver.
  }

  operator phrase pre unary 'log' final : ()() readonly = {
    // `_` is the Color receiver.
  }
}
```

Member declarations cannot resume after definition mode begins. The boundary is
structural rather than a capitalization guess: `MyNested :: type`,
`myAnonymous : :: type`, a function declaration, and an operator declaration do
not match member grammar.

An enum body may contain `final` and `once` functions, operators, nested
definitions, and other declarations that do not expand per-value storage. The
named nested type above is a definition, not a contained enum value. A contained
value with a zero-storage type can be legal because it adds no storage, but an
enum cannot contain a value that makes its representation fat. Compose the enum
into another type when additional stored state is required:

```zax
ColorInfo :: type {
  color : Color
  displayName : String
}
```

This preserves the enum's one-backing-value representation, immediate
projection, raw adoption, size, alignment, and scalar-like lifecycle.

## Backing types

The canonical header places the policy before the optional backing type:

```zax
MyEnum :: enum U8 { }
MyDefaultEnum :: enum { }
MyRelaxed :: enum relaxed U8 { }
MyDefaultRelaxed :: enum relaxed { }
MyFlags :: enum flags U8 { }
MyDefaultFlags :: enum flags { }
```

Omitting the backing type selects:

- `Integer` for strict and relaxed enums; and
- `UInteger` for flags enums.

These defaults are profile-selected
[integer identities](integers.md#profile-selected-integer-families), not fixed
exact intrinsic integers:

```zax
Backing :: alias type MyDefaultEnum underlying type // Integer
```

The exact specialization chosen by the active CPU profile lies beneath the
`Integer` identity boundary.

Eligible backing types are:

- exact intrinsic integer types, including supported unusual widths; and
- language-provided integer roles such as `Short`, `Long`, `Integer`,
  `UInteger`, and `IndexSize`.

An arbitrary user identity does not become eligible merely because it
ultimately uses integer storage. A future identity may become eligible through
an explicit capability.

Flags require an unsigned eligible backing type. Signed flags backing and
negative flag members are errors.

## Admission and reachable values

Three related sets matter:

1. The **ordinary admission domain** contains backing values accepted by safe
   backing-to-enum admission.
2. **Safely reachable values** include everything members, defaults, generated
   behavior, and owner-defined functions or operators may produce.
3. The **representation domain** contains every value representable by the
   backing integer.

### Strict admission

A strict enum ordinarily admits its named values:

```zax
Code :: enum U8 {
  Ready = 1
  Waiting = 2
}

known := Code from (: U8 = 1)

raw : U8 = readByte()
maybeCode := Code optional from raw
```

Plain `from` is available when compile-time facts prove that the exact backing
value names a member. `optional from` checks a runtime backing value and returns
absence when no member has that value.

Owner-defined behavior can still create a defined unnamed value:

```zax
Angle :: enum I16 {
  Zero = 0
  RightAngle = 90

  operator binary '+' final :
    (result : Angle)(rhs : Angle) = existing
}

straight := Angle.RightAngle + Angle.RightAngle
// `straight` is the defined unnamed Angle value 180.
```

Writing `= existing` accepts the adapted backing operation's complete result
behavior without adding validation. If that behavior is too permissive, define
an operation that validates, normalizes, reports failure, or returns another
result shape.

Adding an enum-returning operation does not expand safe admission:

```zax
raw : I16 = straight underlying value
again := Angle optional from raw
// absent: 180 is reachable through + but is not a named admission value
```

### Relaxed admission

A relaxed enum admits every value of its exact immediate backing type:

```zax
AngleValue :: enum relaxed I16 {
  Zero = 0
  RightAngle = 90
}

raw : I16 = readAngle()
angle := AngleValue from raw
```

`optional from` remains available for a uniform generic surface, but it cannot
be absent because of enum admission when its input already has the exact
backing type.

Relaxed admission does not perform implicit widening, narrowing, signedness
change, or identity conversion. Convert to the exact backing type explicitly
first.

## Generated boundary operations

Every enum receives four protected operations:

```zax
BackingType :: alias type EnumType underlying type
raw := enumValue underlying value
semantic := enumValue as BackingType
forced := EnumType unsafe from raw
```

### `underlying type`

`EnumType underlying type` returns the immediate declared or defaulted backing
type. For an enum with omitted backing, that is `Integer` or `UInteger`, not the
profile's deeper exact specialization.

### `underlying value`

`enumValue underlying value` copies out the immediate stored backing value
unchanged.

### `as BackingType`

`enumValue as BackingType` projects the enum's represented value into the exact
immediate backing type. For strict, relaxed, and flags enums, it produces the
same integer as raw extraction.

Direct conversion to another integer type is unavailable:

```zax
raw : U8 = color as U8
wide : U16 = color as U16 // error: U16 is not the backing type

wideViaBacking : U16 = color as U8 as U16
```

The second step uses ordinary
[integer conversion](integers.md#integer-identity-types). Zax does not retain a
special `unsafe as` shortcut from an enum to an unrelated integer.

### `unsafe from`

`EnumType unsafe from raw` accepts the exact backing type, performs no admission
check, and forces that representation into the enum identity:

```zax
raw : U8 = readByte()
forced := Code unsafe from raw
```

This is a defined unsafe permission. An out-of-admission representation becomes
a defined unnamed enum value, and available enum operations handle it according
to their declarations. The programmer deliberately bypasses the ordinary
admission policy; generated string conversion reports absence when no member
names the value.

### Private `underlying place`

The original enum owner receives private `underlying place` whenever code has
an enum receiver:

```zax
(_ underlying place) = raw
```

It is available in the enum's private original-owner context, including
constructors, functions, and operators. It does not become public or cross a
second nested identity boundary.

The owner may use this place to establish a validated or intentionally
reachable unnamed value without routing its own implementation through public
`unsafe from`.

### Structural compatibility remains directional

An enum may serve as a structural compatibility source only for its exact
immediate underlying scalar type. That direction exposes the represented
underlying relationship without granting reverse admission:

- the underlying scalar does not automatically become the enum;
- one enum does not safely become another enum merely because their backing
  formats match; and
- coercive enum-to-enum conversion is available only when the destination
  ordinarily admits every value of its compatible backing representation.

The coercive rule is directional. A relaxed or other fully open-admission
destination may accept a source enum after their backing scalars satisfy the
applicable coercion requirements. A strict, flags, or otherwise restricted
destination does not become coercion-compatible merely because `unsafe from`
could bypass its ordinary admission policy.

Complete posture, shape, layout, and coercion behavior is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#enums-are-directional).

## Comparisons and operation selection

Every enum has backing-value equality and total order. It receives overridable
`= existing` defaults for:

```text
==  !=  <  <=  >  >=  <=>
```

The defaults accept same-enum operands. Duplicate-valued members compare equal.
Different enum identities do not compare merely because their backing types or
values match.

These generated operations are not protected. The owner may replace or forbid
any one independently. Custom comparison implementations may disagree; Zax
does not enforce a comparison algebra beyond ordinary operator selection and
fallback behavior.

A strict enum receives no other backing operators automatically. It may define
one or selectively request a compatible backing implementation:

```zax
operator binary '+' final :
  (result : Angle)(rhs : Angle) = existing
```

Assignment, construction, transfer, replacement, and lifecycle behavior follow
the specialized-identity model unless an enum-specific rule in this document
changes the result.

## Relaxed operation surface

A relaxed enum receives the eligible
[exposed-identity surface](identity-types.md#exposed-and-opaque-behavior) as
overridable `= existing` defaults. This reuses the identity transformation and
filtering rules rather than maintaining a second informal list of "all math
operators."

Results that belong to another domain keep that domain's type. Each supplied
operation may be replaced or forbidden.

## Flags

For a flags enum, the **allowed mask** is the bitwise OR of all member values:

```zax
Permission :: enum flags U8 {
  None = 0
  Read = 1
  Write = 2
  Execute = 4
  ReadWrite = 3
}
```

```text
allowedMask = 0 | 1 | 2 | 4 | 3 = 7
```

A backing value is safely admissible exactly when:

```text
(value & ~allowedMask) == 0
```

Zero and every submask are therefore admitted. A combination need not have a
member name. Duplicate values, aliases, composites, and overlapping member
masks are allowed. An empty flags enum has an allowed mask of zero and safely
admits only zero.

```zax
raw : U8 = readPermissionBits()
maybePermission := Permission optional from raw
```

### Generated flags operations

Flags supply overridable `= existing` defaults for operations that cannot
introduce bits absent from valid operands:

```text
|    &    ^    &~
|=   &=   ^=   &~=
```

They do not automatically supply:

- raw complement or complement assignment;
- NAND, NOR, or XNOR;
- shifts or rotations; or
- integer reductions and unrelated arithmetic.

Those operations can introduce bits outside the mask or change the meaning of
bit positions. Availability does not vary merely because one enum happens to
cover every bit of its current backing width.

Generated flags operations are not protected. They may be overridden or
forbidden, and the owner may declare additional behavior. An override standing
in for one of the generated operations is responsible for preserving that
operation's mask contract. A domain that needs fundamentally different behavior
uses an ordinary or relaxed enum and declares its policy directly.

`unsafe from` may force an outside-mask backing value. The value remains defined,
but the caller has bypassed the safe flags contract. The generated closure
guarantee applies to safely admitted operands.

## Type-callable `once` functions

A `once` function declared in a type has one type-owned implementation shared by
all instances:

```zax
fromString final once : (
  result : Color?
)(
  source : String readonly &
) = default
```

It may be called through the type or an instance:

```zax
maybeFromType := Color.fromString(text)
maybeFromValue := color.fromString(text)
```

On a receiverless type call, `_` identifies the enum type's Nothing instance
and `?_` is false. On an instance call, `_` identifies that instance and `?_` is
true. `final` prevents reassignment under ordinary function rules.

This narrow callable rule does not decide global or `once` value initialization,
concurrency, teardown, capture, or generic-specialization behavior.

## Generated string conversion

String conversion compares declared ASCII member names. It performs no numeric,
separator, locale, or domain-specific parsing.

Every enum receives generated `fromString` and
`fromStringIgnoringCase` functions when the owner does not provide the same
signature:

```zax
fromString final once : (
  result : Color?
)(
  source : String readonly &
) = default

fromStringIgnoringCase final once : (
  result : Color?
)(
  source : String readonly &
) = default
```

Representative calls are:

```zax
maybeExact := Color.fromString(text)
maybeFolded := Color.fromStringIgnoringCase(text)
```

Exact lookup returns the value of an exactly matching member or absence.
Duplicate values under different names do not make exact lookup ambiguous.

### ASCII case-insensitive lookup

Case-insensitive lookup folds ASCII letter case:

- case-equivalent member names with the same value are unambiguous;
- case-equivalent names with different values require intent acknowledgement;
- acknowledgement permits the declaration but does not select one result; and
- unique case-insensitive lookup returns absence for an acknowledged ambiguous
  folded name.

```zax
// Same folded name and same value: no acknowledgement required.
BoxKind :: enum U8 {
  BlueBox = 1
  Bluebox = 1
}
```

Different values use the exact intent category around the complete declaration:

```zax
intent<case-conflicting-enum-member-names>{
  BoxKind :: enum U8 {
    BlueBox = 1
    Bluebox = 2
  }
}
```

Exact lookup can still distinguish `"BlueBox"` and `"Bluebox"`.
`BoxKind.fromStringIgnoringCase("bluebox")` returns absence because the folded
name identifies two distinct values.

### Enum value to string

Strict and relaxed enums receive:

```zax
toString final : (
  result : String?
)() readonly = default
```

It returns the name of the first declared member whose value equals the receiver.
It returns absence for an unnamed value:

```zax
maybeName := color.toString()
```

Flags do not receive generated `toString` or string-array decomposition.
Aliases, composites, overlaps, and unnamed combinations have no universal
decomposition policy.

### Several string inputs for flags

Flags need bulk conversion operations conceptually named `fromStrings` and
`fromStringsIgnoringCase`. [Zax iteration](iteration.md) now defines how a
concrete collection or cursor is traversed, but the exact input type and
declaration remain deferred until Zax has a suitable collection parameter or
generic iterable constraint:

```text
fromStrings(sequence of String) -> Permission?
fromStringsIgnoringCase(sequence of String) -> Permission?
```

Each input must resolve to one distinct member value. Zax ORs those values. An
unknown or case-insensitively ambiguous element makes the complete conversion
absent; it does not return a partial value or OR every ambiguous candidate.

A singular flags `fromString` resolves one member name only. It does not parse a
numeric value or textual composition.

### Replacement and suppression

Generated string functions are not protected:

- an absent applicable declaration receives the generated candidate on demand;
- `= default` explicitly requests generation;
- a declaration body replaces the generated implementation; and
- `= forbidden` disables that signature.

Demand generation does not require unused lookup tables or executable code to
be materialized.

<a id="deferred-enum-traversal"></a>

## Enum declaration traversal

`each in` visits every declared member in source order and preserves
duplicate-valued aliases:

```zax
Box :: enum U8 {
  RedBox = 1
  Redbox = 2
  GreenBox = 5
  Greenbox = 5
}

each value : in Box {
  print(value as underlying value)
}
// 1, 2, 5, 5
```

The current roles are enum `value` followed by declared ASCII `name`:

```zax
each value :, memberName : in Box {
  print(value as underlying value, memberName)
}

each (name: memberName :) in Box {
  print(memberName)
}
```

An inferred name is a compiler-provided
`String readonly immutable final &`. An explicit `String` requests a copy. A
future reflection role follows `value` and `name`, but no reflection binding or
metadata type is current.

There is no dedicated case-insensitive match traversal. Generated
`fromStringIgnoringCase` remains the built-in singular lookup. Code needing
several or domain-specific matches traverses declarations and applies its own
matching and deduplication.

Member traversal supplies the facts needed for programmer-defined flags
formatting and decomposition. It does not choose a decomposition policy.
Complete binding, flow, and cursor behavior is owned by
[Zax iteration](iteration.md#enum-declarations). General reflection remains
separate future work.

## Selection with enum values

Member names do not necessarily cover every enum value:

- a strict owner operation may produce an unnamed value;
- relaxed enums admit every backing value;
- flags admit unnamed submasks;
- `unsafe from` may force any backing value;
- duplicate-valued member cases overlap; and
- an empty strict enum can default to an unnamed value.

Runtime selection therefore cannot treat a list of member names as exhaustive
merely because an enum is strict. It audits two dimensions separately:

1. **Declared-member coverage:** every distinct declared member value is
   explicitly handled for the applicable search entry.
2. **Reachable-domain coverage:** every other reachable backing value can select
   a body.

```zax
switch permissions {
  case Permission.Read
    allowRead()
  case Permission.Write
    allowWrite()
  default
    handleOtherPermissionValue()
}
```

The two declared members are explicit. `default` covers unnamed flags
combinations, owner-produced unnamed values, and unsafe-admitted backing values
not selected earlier. It does not silently count as explicit handling of a later
declared `Permission.Execute`.

A deliberately partial member selection requires acknowledgement:

```zax
intent<partial-enum-selection>{
  switch status {
    case Status.Ready
      startWork()
    default
      handleOtherDeclaredOrUnnamedStatus()
  }
}
```

The acknowledgement permits omitted declared values. It does not establish
complete backing-domain coverage or change which operation each case selects.

A selection without `default` remains domain-incomplete unless its actual tests
cover every reachable value. A selection with `default` remains member-incomplete
when it omits a distinct declared member without
`intent<partial-enum-selection>{...}`.

Under generated equality, duplicate-valued member names select the first matching
case. A later duplicate-valued case is semantically unreachable and requires
`intent<unreachable-selection-clause>{...}` when deliberately retained. Replaced
equality may behave differently; reachability follows the operation actually
selected rather than member declarations alone.

Duplicate-valued aliases count as one distinct declared value for member
coverage. Flags and relaxed enums still admit unnamed values beyond that member
set.

Coverage is assessed for every reachable ordered-search entry. A later test
segment reached through `continue` does not retroactively complete the initial
segment. Arbitrary user-defined predicates contribute only when the language can
prove the values they cover.

Only a reachable test contributes declared-member coverage. `goto` can make a
case body reachable while bypassing its test, so it contributes no member
coverage. Likewise, a member case physically following a positional default does
not count for a search that can never reach its test.

Complete case ordering, test interpretation, transfers, and diagnostics are
defined by [Zax switch, case, and default](switch.md).

## Costs

- An enum adds identity but no per-value storage beyond its backing integer.
- Member use, default comparisons, raw extraction, and ordinary projection can
  require no representation change.
- Strict `optional from` may compare against member values or use a generated
  lookup.
- Flags admission requires an allowed-mask check.
- Relaxed admission requires no membership check.
- String conversion may require generated name data and lookup work when used.
- Multi-string flags admission performs lookup and OR work and fails atomically.
- No general reflection or runtime metadata table is implied merely by declaring
  an enum.

## Diagnostics

Diagnostics should distinguish:

- an ineligible backing type;
- signed backing for flags;
- a nonconstant or nonrepresentable member value;
- implicit member progression beyond the backing range;
- an omitted flags member value;
- duplicate member names;
- multiple default markers;
- a comma in the member prologue;
- a member declaration after definition mode begins;
- a stored per-value member;
- implicit integer-to-enum transfer;
- failed strict or flags admission;
- raw adoption from a non-backing type;
- direct conversion to a non-backing integer;
- an unavailable, forbidden, or mismatched enum operation;
- comparison or assignment across unrelated enum identities;
- a switch omitting a distinct declared member value without
  `intent<partial-enum-selection>{...}`;
- an unacknowledged case-conflicting member name;
- ambiguous unique case-insensitive conversion;
- an unknown or ambiguous flags string input; and
- an unnamed value producing absence from `toString`.

## Source stability

- Inserting or changing a member can renumber later implicit members.
- Changing the first member or explicit marker can change ordinary default
  initialization.
- Changing a flags marker can change its default while zero remains admitted.
- Adding or changing a flags member can change its allowed mask.
- Reordering aliases can change `toString`'s first-name result.
- Adding a case-conflicting name may require intent acknowledgement or make
  unique case-insensitive lookup return absence.
- Adding owner behavior may make unnamed values reachable.
- Adding a distinct declared member makes a previously member-complete
  unacknowledged switch incomplete even when that switch has `default`.
- Replacing or forbidding generated behavior changes source availability.
- Changing an enum's immediate backing can change range, representation, cost,
  and generated signatures.
- Changing a CPU profile may change representation of an enum backed by a
  profile-selected integer role without changing its immediate source type.

Use explicit backing and member values when values cross persistence, protocol,
or foreign boundaries. Equal storage is not yet an ABI or FFI guarantee.

## Boundaries and maturity

This document defines current conceptual enum behavior. It is not a formal
grammar, ABI contract, serialization specification, or implementation mapping.

Still deferred:

- `each` syntax and compiler-known traversal;
- first-class iterator values, iterable concepts, and user-defined protocols;
- broader compile-time production of member constants;
- general reflection and metadata;
- generic and generated enum families;
- open enum extension beyond the current rule that a
  [partial](partials.md#enums-variants-and-unions) may add functions and
  nested declarations to an enum but never members;
- broader eligibility for user-defined backing identities;
- fat identity or enum representation;
- generalized pattern matching and variant payload binding; current enum use in
  `switch` is defined by
  [switch, case, and default](switch.md#enum-coverage-has-two-dimensions);
- ABI, FFI, and persistence contracts; and
- compiler lowering.
