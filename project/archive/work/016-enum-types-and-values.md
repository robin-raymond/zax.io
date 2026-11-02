# 016: Enum types and values

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `016` |
| Created | 2026-09-07 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | General reflection, generics, computed members, complete selection/pattern matching, partial enum extension, ABI/FFI contracts, or compiler lowering |

## Non-authority notice

This file is a historical working record retained for targeted audits. Its
aligned findings were promoted into current owners before archival. The record
remains non-normative and is not part of ordinary onboarding or current language
authority.

## Fixed initiating input

This section records the information aligned when work item `016` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for enum types and values:

1. enum declaration and member source;
2. enum type and member identity;
3. ordinary enums versus flags/bitset-like enums;
4. optional explicit underlying integer types;
5. valid, invalid, and unknown underlying values;
6. default initialization;
7. construction, admission, projection, and conversion;
8. equality, ordering, bitwise, and generated operation availability;
9. exhaustive-selection pressure without designing complete pattern matching;
10. representation, portability, costs, and diagnostics;
11. interaction with existing endian semantic enums; and
12. disposition of legacy enum syntax and behavior.

### Motivating pressure

Zax has current integer, identity, operator, and endian behavior that refers to
enums, but no cohesive current enum owner. Programmers need to understand forms
such as:

```zax
// Illustrative starting shape; exact enum syntax requires review.
Color :: enum {
  Red
  Green
  Blue
}

color : Color = Color.Red
```

The language must explain whether every underlying bit pattern is a valid enum
value, how explicit underlying values behave, which operations are generated,
and how ordinary semantic enums differ from flags intended for bitwise
composition.

### Known assumptions

- Enum values have a concrete enum type identity rather than behaving as
  untyped integer constants.
- Existing current endian semantic enum families and the four generated
  underlying/admission operations are evidence and constraints.
- An underlying integer representation does not automatically grant every
  integer operation to the enum identity.
- Representation and value validity are distinct concerns.
- Current work may identify reflection or generic pressure without designing
  those mechanisms.
- There is no language implementation in this repository.

### Known inclusions

- Ordinary enum declaration and member mental model.
- Member identity and explicit values.
- Underlying integer selection and defaults.
- Default enum value behavior.
- Validity of unnamed underlying values.
- Construction, admission, projection, and conversion.
- Generated equality, ordering, and applicable bitwise operations.
- Ordinary versus flags-style behavior.
- Interaction with integer identities and endian semantic enums.
- Exhaustiveness pressure needed to constrain future selection work.
- Costs, diagnostics, portability, and source stability.
- Lasting documentation ownership and legacy disposition.

### Known starting boundaries

- General compile-time reflection and enum-to-string metadata.
- Generic or generated user enum families.
- Computed or runtime-dependent enum members.
- Complete `switch`, pattern matching, and exhaustiveness syntax.
- Partial/open enum extension.
- Formal layout, ABI, FFI, or compiler lowering.
- Serialization frameworks and schema generation.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Exact enum declaration and member syntax.
- Whether explicit values are required, optional, or mixed.
- Default underlying integer type.
- Whether ordinary enums admit unnamed underlying values.
- Whether flags are a separate declaration kind, policy, or ordinary enum
  contract.
- Default initialization and whether one member is distinguished as default.
- Which operations are generated versus unavailable.
- Ordering semantics for ordinary enums.
- How unsafe/raw admission differs from ordinary construction.
- How exhaustive selection treats unknown or unnamed values.

### Initial stopping guidance

Stop when the work has:

- established a usable enum declaration and value model;
- established ordinary and flags-style boundaries;
- established underlying representation and value-validity rules;
- established construction, conversion, and generated operation behavior;
- established default initialization;
- constrained future exhaustive selection without designing it;
- dispositioned legacy enum evidence;
- identified exact current owners and indexed deferrals; and
- completed the required documentation-fit dry run.

Do not design reflection, generics, complete selection, partial enum extension,
ABI/FFI, promote findings, archive this work item, or begin work item `017`
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- [Current enum owner](../../language/enums.md) - contains the aligned promoted
  declaration, member, admission, flags, conversion, and operation model.
- Historical source paths `enums.md` and `project/raw/enum-types.md` supplied the
  initial evidence and were retired by promotion; use Git history only for a
  targeted provenance audit.
- Focused [generated underlying and enum forms](../../language/operator-catalog.md#generated-underlying-and-enum-forms) -
  supplies current generated operation names and protected behavior.
- Focused [endian semantic enum families](../../language/endianness.md#endian-semantic-enum-families)
  and [generated enum operations](../../language/endianness.md#the-four-generated-enum-operations) -
  supply current concrete enum use and receiver-correct behavior.
- Focused [enum and endian boundaries](../../language/integers.md#enum-and-endian-boundaries)
  and [integer identity types](../../language/integers.md#integer-identity-types) -
  constrain underlying representation without granting integer membership.
- Focused [identity boundary and underlying type](../../language/identity-types.md#identity-boundary-and-underlying-type),
  [admission](../../language/identity-types.md#admission), and
  [construction and transfer](../../language/identity-types.md#construction-and-transfer) -
  supply distinct-identity, admission, projection, and construction pressure.
- Focused [non-value definitions](../../language/declarations-and-bindings.md#non-value-definitions),
  [direct initialization](../../language/declarations-and-bindings.md#direct-initialization),
  and [default initialization](../../language/declarations-and-bindings.md#default-initialization) -
  supply declaration and value-initialization integration.

### Consequence-driven

- Read [raw selection input](../raw/selection.md) when enum exhaustiveness,
  unknown values, or flags create immediate selection constraints.
- Read [raw reflection input](../raw/reflection.md) when member iteration,
  names, strings, or metadata become necessary.
- Read [raw generic input](../raw/type-parameters-and-generics.md) when a proposal
  requires generic enum families or constraints.
- Read [raw partial-type input](../raw/partial-types.md) when enum extension or
  externally added members becomes concrete.
- Read [raw interop input](../raw/interop.md) when underlying representation
  requires ABI or foreign-language guarantees.
- Read focused construction, safety, source, or operator material only when a
  concrete enum rule crosses that owner's boundary.

### Audit-only

- `project/archive/`, including work items `001` through `015`.
- Deleted or superseded enum implementation sketches recoverable through Git
  history.

Do not read archived work item `015` during ordinary work on `016`. Its accepted
findings are promoted into current owners and its future pressures are preserved
in live raw inputs.

## Working record

> **Working maturity:** Sections explicitly labeled as aligned findings record
> maintainer alignment for this work item. They remain non-authoritative until
> separately promoted into their lasting owners. The superseded initial
> reconstruction remains evidence only.

### Aligned findings

#### Programmer model

An enum is a specialized identity over an eligible integer backing type. It
presents that backing representation as a distinct enum type, optionally gives
known values names, and owns the behavior available through the new identity.

```zax
Color :: enum U8 {
  Red
  Green
  Blue
}

color : Color = Color.Red
raw : U8 = color underlying value

color = raw // error: no implicit crossing of the enum identity
```

`Color.Red` is a value of `Color`, not an uncommitted integer or a distinct
member-specific type. Two member names with the same backing value denote equal
enum values:

```zax
Fruit :: enum U8 {
  Apple = 4
  Grapefruit = 4
}

same := Fruit.Apple == Fruit.Grapefruit // true
order := Fruit.Apple <=> Fruit.Grapefruit // equal
```

An enum does not automatically inherit the backing integer's operators. It
receives the enum defaults established below, may selectively adapt an eligible
backing operation through `= existing`, and may define its own functions and
operators.

Three declaration policies distinguish ordinary admission and generated
behavior:

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

- A plain enum is **strict**: ordinary safe admission accepts its named member
  values.
- A `relaxed` enum safely admits every value of its backing type and receives
  the eligible exposed-identity operation surface through overridable
  `= existing` defaults.
- A `flags` enum safely admits every submask of its declared allowed mask and
  receives a closed allow-list of bitwise operations.

`relaxed` and `flags` are mutually exclusive policies. A plain `enum` needs no
`strict` keyword.

#### Backing type

The canonical header order is:

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

These are profile-selected integer identities rather than exact intrinsic
integers. The enum's immediate underlying type remains the written or defaulted
integer role:

```zax
Backing :: alias type MyDefaultEnum underlying type // Integer
```

The profile-selected exact intrinsic representation remains beneath that
integer identity boundary. Enum backing therefore need not itself be an exact
intrinsic.

Eligible explicit backing types are:

- exact intrinsic integer types, including unusual widths supported by the
  integer model; and
- language-provided integer roles such as `Short`, `Long`, `Integer`,
  `UInteger`, and `IndexSize`.

An arbitrary user-defined identity does not become eligible merely because it
ultimately has integer storage. Endian semantic enums are not eligible backing
types for another enum. Future work may cautiously expand eligibility through
an explicit capability rather than representation coincidence.

Flags require an unsigned eligible backing type. Zero and other non-negative
member values are allowed. Negative flag members and signed flags backing are
rejected.

#### Member prologue and body

An enum body begins with a contiguous member prologue. An ordinary or relaxed
member has this shape:

```text
UpperName [= integer constant expression] [default]
```

Examples:

```zax
Status :: enum I8 {
  Ready
  Waiting = 4
  Paused = (
    2 + 3
  ) default
}
```

- Member declarations do not use commas.
- A member name and a following `=` or `default` remain on the same effective
  statement; an uncontinued newline ends the member.
- The value expression follows ordinary expression-continuation rules.
- `default`, when present, follows the name or completed value expression with
  separating whitespace.
- A member value must resolve during enum definition to an integer constant
  representable by the backing type. General compile-time-produced constants
  remain future compile-time-execution integration.
- A nonrepresentable explicit value is a non-acknowledgeable error.

For strict and relaxed enums, the first implicit member value is zero. Each
later implicit member is the preceding member's value plus one, including after
an explicit value:

```zax
Sequence :: enum I8 {
  First       // 0
  Tenth = 10
  Eleventh    // 11
}
```

Implicit progression that exceeds the backing range is a non-acknowledgeable
compile-time error; it never wraps.

Every flags member requires an explicit value. Sequential `+ 1` assignment
would obscure flag intent:

```zax
Permission :: enum flags U8 {
  Read = 1
  Write = 2
  Execute = 4
  ReadWrite = 3
}
```

Member names are unique. Duplicate backing values, composite flags, and
overlapping flags are allowed. All enum members are public; individual member
visibility is not configurable.

The first complete statement that does not match member grammar ends the member
prologue and begins ordinary definition mode. Member declarations cannot resume
later:

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

The distinction is structural rather than a lowercase-token heuristic.
`MyNested :: type`, `myAnonymous : :: type`, a function declaration, and an
operator declaration cannot be mistaken for a member.

The body may contain `final` and `once` functions, operators, nested
definitions, and other declarations that do not expand per-value storage. The
named nested type above is a definition rather than a contained enum value. A
contained value with a zero-storage type can be legal because it adds no
storage, but an enum cannot contain a value that makes its representation fat. A
programmer who needs additional stored state composes the enum into another
type:

```zax
ColorInfo :: type {
  color : Color
  displayName : String
}
```

This restriction preserves the enum's one-backing-value representation,
immediate projection, raw adoption, size, alignment, and scalar-like lifecycle.
Whether identity-like declarations can ever expand representation remains
future owned-composition work rather than an enum exception.

#### Defaults

For strict and relaxed enums:

- at most one member may carry `default`;
- an explicit marker selects that member;
- otherwise the first member is the default; and
- an enum with no members defaults to the backing type's default value.

```zax
Fruit :: enum I8 {
  Apple = 4
  Orange = 42 default
}

fruit : Fruit // Fruit.Orange
```

For flags:

- no explicit marker means zero is the default, whether or not zero has a name;
- one declared member may carry `default`;
- the marked member may have any safely admitted value, including a nonzero or
  composite value; and
- more than one marker is an error.

```zax
Permission :: enum flags U8 {
  None = 0
  Read = 1
  Write = 2
  Typical = 3 default
}

permission : Permission // Permission.Typical
```

Zero remains safely admissible even when another flags value is the default.

Default initialization is an enum construction operation. It need not imply
that the same backing value is ordinarily admissible. In particular, an empty
strict enum defaults to the backing type's default, but its named safe-admission
set is empty:

```zax
Empty :: enum U8 {
}

value : Empty // defined unnamed value backed by zero
raw : U8 = 0
maybe := Empty optional from raw // absent
```

#### Admission, reachability, and representation

Enum design distinguishes three sets:

1. **Ordinary admission domain:** backing values accepted through safe
   backing-to-enum admission.
2. **Safely reachable values:** values that members, defaults, generated
   behavior, and owner-defined functions or operators may produce.
3. **Representation domain:** every value representable by the backing integer.

Strictness controls ordinary admission. It does not make other backing states
non-values after an authorized operation or unsafe adoption creates them.

For a strict enum:

- safe admission accepts named member values;
- compile-time-proven named backing values may use ordinary `from`;
- `optional from` validates a runtime backing value against the named values;
- owner behavior and selectively enabled `= existing` operations may produce
  defined unnamed values; and
- adding such behavior does not silently broaden ordinary backing admission.

```zax
Angle :: enum I16 {
  Zero = 0
  RightAngle = 90

  operator binary '+' final :
    (result : Angle)(rhs : Angle) = existing
}

straight := Angle.RightAngle + Angle.RightAngle
// `straight` is the defined unnamed Angle value 180.

raw : I16 = straight underlying value
again := Angle optional from raw
// `again` is absent: operator reachability did not broaden ordinary admission.
```

`= existing` means the programmer accepts the adapted underlying operation's
complete result behavior. It adds no validation. When that behavior is too
permissive, the enum owner defines an operation that validates, normalizes,
reports failure, or returns a different result shape.

For a relaxed enum:

- every backing value is ordinarily and safely admissible;
- ordinary `from` accepts every exact backing value;
- `optional from` cannot be absent because of enum admission when given the
  exact backing type; and
- the eligible underlying exposed-identity operation suite is supplied as
  overridable `= existing` behavior.

For flags, ordinary admission follows the allowed-mask rule defined below.

Safe admission accepts the enum's exact immediate backing type. It does not
silently widen, narrow, change signedness, or cross another identity. The
programmer performs any integer conversion first.

#### Generated underlying operations and owner authority

Every enum retains the four generated protected boundary operations:

```zax
BackingType :: alias type EnumType underlying type
raw := enumValue underlying value
semantic := enumValue as BackingType
forced := EnumType unsafe from raw
```

- `underlying type` returns the immediate declared or defaulted backing type.
- `underlying value` returns the stored immediate backing value unchanged.
- `as BackingType` performs the enum's semantic projection to that exact type.
  For ordinary enums it produces the same integer as raw extraction.
- `unsafe from` accepts the exact backing type, performs no admission check, and
  forces that representation into the enum identity.

`unsafe from` is a defined unsafe permission, not a membership assertion whose
failure makes the resulting bits uninterpretable. A forced out-of-admission
value is a defined unnamed enum value. Available enum operations must handle it
according to their declared behavior; `toString` reports absence when no member
names it.

For flags, `unsafe from` can likewise force bits outside the allowed mask. The
caller deliberately bypasses the safe mask contract. Language-generated
bitwise closure is guaranteed for safely admitted operands, not for a forced
outside-mask operand.

Direct conversion to a non-backing integer remains unavailable. Conversion
proceeds explicitly through the immediate backing:

```zax
raw : U8 = color as U8
wide : U16 = color as U16 // error: U16 is not the immediate backing type
wideViaBacking : U16 = color as U8 as U16
```

The legacy `unsafe as` shortcut to an unrelated integer is not retained.
Ordinary integer conversion owns the second step and its exact, optional, or
narrowing policy.

The original enum owner also receives private `underlying place` whenever code
has an enum receiver:

```zax
(_ underlying place) = raw
```

It is available throughout the enum's private original-owner context, including
constructors, functions, and operators. It does not become public and does not
cross an additional nested identity boundary. Owner code may use it to establish
validated or intentionally reachable unnamed values without routing through the
public `unsafe from` spelling.

#### Comparisons and ordinary operation selection

Every enum has backing-value equality and total order. It receives overridable
`= existing` defaults for:

```text
==  !=  <  <=  >  >=  <=>
```

The defaults adapt the immediate backing operations to same-enum operands.
Duplicate-valued members compare equal and `<=>` reports equality. Values of
different enum identities do not compare merely because their backing types or
values match.

These generated operations are not protected. The enum owner may replace or
forbid any one independently. Custom implementations are permitted to disagree;
Zax does not enforce comparison algebra or regenerate one operation from
another beyond the general operator fallback rules.

Strict enums receive no other backing operators automatically. The owner may
declare a custom operation or selectively request compatible existing behavior:

```zax
operator binary '+' final :
  (result : Angle)(rhs : Angle) = existing
```

Relaxed enums receive the complete eligible surface defined by identity
exposure, rather than a separately invented "all math operators" list. Results
that belong to another domain retain that domain's type. Each supplied operation
may be replaced or forbidden.

Assignment, construction, transfer, replacement, and lifecycle behavior follow
the specialized-identity model unless an enum-specific rule above changes the
result.

#### Flags

For:

```zax
Permission :: enum flags U8 {
  None = 0
  Read = 1
  Write = 2
  Execute = 4
  ReadWrite = 3
}
```

the enum's allowed mask is the bitwise OR of every declared member value:

```text
allowedMask = 0 | 1 | 2 | 4 | 3 = 7
```

A backing value is ordinarily admissible exactly when:

```text
(value & ~allowedMask) == 0
```

Therefore zero and every submask are admitted. A value need not have a member
name. Duplicate values, aliases, composites, and overlapping masks are allowed.
An empty flags enum has an allowed mask of zero and safely admits only zero.

The flags declaration supplies overridable `= existing` defaults for the
bitwise operations that cannot introduce bits absent from valid operands:

```text
|    &    ^    &~
|=   &=   ^=   &~=
```

It does not automatically supply:

- raw complement or complement assignment;
- NAND, NOR, or XNOR;
- shifts or rotations; or
- integer reductions and unrelated arithmetic.

Those operations can introduce bits outside the allowed mask or change the
meaning of bit positions. Availability does not vary merely because one
particular enum happens to cover every bit of its current backing width.

Generated flags operations are not protected. They may be overridden or
forbidden, and the enum may declare additional functions and operators. An
override standing in for a generated flags operation is responsible for
preserving that operation's flags-mask contract. A domain that intentionally
needs different general behavior should use an ordinary or relaxed enum and
declare its policy directly.

#### Generated string conversion

String conversion is generated on demand and owns no fancy parsing. It compares
declared ASCII member names. All member names are public.

Conceptually, a `Color` enum receives these non-protected default functions when
their signatures are otherwise absent:

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

The `once` qualifier gives a function declared inside a type one type-owned
implementation shared by all instances. It is callable through either the type
or an instance:

```zax
maybeFromType := Color.fromString(text)
maybeFromValue := color.fromString(text)
```

On a type call, `_` has the `Nothing` instance state. On an instance call, `_`
is that instance. `final` prevents reassignment under ordinary function rules.
This narrow callable rule does not settle global or `once` value initialization,
concurrency, teardown, capture, or generic-specialization behavior.

Ordinary and relaxed enums additionally receive:

```zax
toString final : (
  result : String?
)() readonly = default
```

Representative calls are:

```zax
maybeColor := Color.fromString(text)
maybeColor := Color.fromStringIgnoringCase(text)
maybeName := color.toString()
```

The exact-case converter returns the member value for one exactly matching
declared name and otherwise returns absence. Duplicate backing values do not
make distinct names ambiguous.

Case-insensitive conversion uses ASCII case folding only:

- case-equivalent declarations with the same enum value are unambiguous;
- case-equivalent declarations with different enum values create an
  acknowledgeable intent error at the enum declaration;
- after acknowledgement, unique case-insensitive conversion returns absence for
  that ambiguous folded name; and
- the acknowledgement permits the declarations but does not select a winner.

```zax
// Same folded name and same value: no ambiguity.
BoxKind :: enum U8 {
  BlueBox = 1
  Bluebox = 1
}
```

Different values require an acknowledgement around the complete enum
declaration:

```zax
intent<case-conflicting-enum-member-names>{
  BoxKind :: enum U8 {
    BlueBox = 1
    Bluebox = 2
  }
}
```

The category applies equally to strict, relaxed, and flags enums. Exact-case
lookup remains usable after acknowledgement. Unique case-insensitive lookup
returns absence for `bluebox`, while the multi-value lookup yields the distinct
values in declaration order.

For ordinary and relaxed enum-to-string conversion:

- the first declared member whose value equals the receiver supplies the name;
- duplicate-valued later declarations do not replace it; and
- an unnamed value returns absence.

Flags receive the singular exact and case-insensitive lookup functions. A
singular lookup resolves one declared name only; it does not parse numeric text,
separators, or a textual flag composition.

Flags need future bulk conversion operations conceptually named `fromStrings`
and `fromStringsIgnoringCase`. Their exact input type and declaration remain
deferred until a suitable concrete collection or iterable contract exists:

```text
fromStrings(sequence of String) -> MyFlags?
fromStringsIgnoringCase(sequence of String) -> MyFlags?
```

Each element must resolve to one distinct member value. The resolved values are
ORed. An unknown or case-insensitively ambiguous element makes the entire
conversion absent; no partial flag value is returned, and ambiguity is never
interpreted as a request to OR all candidates.

Flags receive no generated `toString` or string-array decomposition. Aliases,
composites, overlaps, and unnamed combinations prevent one universal
decomposition policy.

These generated functions are supplied only when the enum owner does not
provide the same signature. The owner may replace one or write `= forbidden`.
`= default` explicitly requests the generated implementation. Demand generation
requires no separate source request and need not materialize unused conversion
tables or code.

#### Generated traversal pressure

Two traversal capabilities are required without assuming first-class iterator
values before concepts can express their contracts:

1. Member traversal visits every declaration in source order, preserves
   duplicate-valued aliases, and exposes at least the declared ASCII name, enum
   value, and declaration order.
2. Case-insensitive match traversal visits each distinct matching enum value
   once, in the order of its first matching declaration.

An initial `each` design may provide compiler-known traversal directly:

```zax
// Illustrative only; exact `each` syntax remains future work.
each member : in Color {
  inspect(member)
}
```

Future concepts may enable equivalent first-class iterator-returning functions.
Exact `each` syntax, compiler-known sources, iterator values, iterable string
input, and user-defined protocols remain future work. That work must return to
the enum owner with concrete declarations and examples.

General enum reflection, metadata, and enum-to-string customization remain
separate from these narrow traversal requirements.

#### Exhaustive-selection pressure

Every representable backing value becomes a defined enum value once an
authorized operation or `unsafe from` produces it. Ordinary admission does not
determine every possible runtime enum value.

```zax
Angle :: enum I16 {
  Zero = 0
  RightAngle = 90

  operator binary '+' final :
    (result : Angle)(rhs : Angle) = existing
}

straight := Angle.RightAngle + Angle.RightAngle // unnamed value 180
```

Future selection therefore cannot treat a list of member names as exhaustive
merely because the enum is strict:

- duplicate-valued member cases overlap;
- strict owner behavior may produce unnamed values;
- relaxed enums admit every backing value;
- flags admit unnamed submasks;
- `unsafe from` produces defined unnamed values; and
- empty strict enums can default to an unnamed backing-default value.

A selection over enum values needs a catch-all or another future mechanism that
covers the remaining backing states unless its cases actually cover every
possible value. Complete `switch`, pattern, guard, fallthrough, transfer, and
diagnostic syntax remains selection work.

#### Endian semantic enums

A programmer-defined enum cannot use an endian semantic enum as its backing
type:

```zax
MyEnum :: enum BigEndianU32 { } // error: ineligible backing type
```

Language-supplied endian types remain semantic enums backed directly by eligible
intrinsic integers. They demonstrate why raw extraction and semantic projection
are distinct:

- `underlying value` returns stored byte arrangement;
- `as UnderlyingType` decodes represented numeric meaning;
- destination-owned `from` performs semantic encoding or transcoding; and
- every value of the intrinsic backing type is admitted.

The endian owner continues to define its domain-specific focused operations.
Generated endian family names and their generic production mechanism remain
future numeric/generic work.

#### Costs, diagnostics, and source stability

An enum adds identity but no per-value storage beyond its backing integer.
Size, alignment, and representation extent follow the immediate backing type's
applicable guarantees. Formal ABI and FFI contracts remain future interop work.

Expected costs include:

- member use, comparison defaults, raw extraction, and ordinary projection can
  require no representation change;
- strict `optional from` may compare against member values or use a generated
  lookup;
- flags `optional from` requires an allowed-mask check;
- relaxed admission requires no membership check;
- string conversion may require generated name data and lookup work only when
  demanded;
- flags string-array admission performs lookup and OR work and fails atomically;
  and
- endian semantic conversion may require byte rearrangement.

Required diagnostics include:

- an ineligible backing type;
- signed backing for flags;
- a nonrepresentable or nonconstant member value;
- implicit ordinary-member progression beyond the backing range;
- an omitted explicit flags member value;
- a duplicate member name;
- multiple `default` markers;
- a comma in the member prologue;
- a member declaration after definition mode begins;
- a stored per-instance enum member;
- implicit integer-to-enum transfer;
- failed strict or flags admission;
- raw adoption from a type other than the exact immediate backing;
- direct enum conversion to a non-backing integer;
- an unavailable, forbidden, or mismatched enum operation;
- comparison or assignment across unrelated enum identities;
- a case-fold collision whose declarations have different values unless
  acknowledged;
- ambiguous unique case-insensitive conversion;
- an unknown or ambiguous flags string element; and
- enum-to-string conversion of an unnamed value through absence rather than a
  fabricated name.

Source-stability consequences include:

- inserting or changing a member can renumber later implicit members;
- changing the first member or explicit marker can change ordinary default
  initialization;
- changing a flags marker can change its default while zero remains admitted;
- adding or changing a flags member can expand or contract the allowed mask;
- reordering duplicate-valued members changes `toString`'s first-name result;
- adding a case-fold-conflicting name may introduce an intent diagnostic or make
  unique case-insensitive conversion fail;
- adding `= existing`, relaxed exposure, or owner behavior may make unnamed
  values reachable and affect selection;
- replacing or forbidding a generated operation changes source availability;
- changing an enum's immediate backing role may change representation, range,
  cost, and generated signatures; and
- changing a CPU profile may change representation of an enum backed by a
  profile-selected integer role without changing the immediate source type.

#### Legacy disposition aligned for eventual promotion

| Legacy evidence | Aligned disposition |
| --- | --- |
| `Name :: enum [UnderlyingType] { ... }` | Retain the non-value declaration family with the canonical strict, relaxed, and flags header order. |
| Comma-separated members | Supersede with newline/effective-statement member declarations without commas. |
| Implicit values begin at zero and increment | Retain for strict and relaxed enums, including continuation after an explicit value; diagnose overflow. |
| Flags may omit values | Reject; every flags member requires an explicit value. |
| First member is the default | Retain for nonempty strict and relaxed enums when no marker is present. |
| `default` before `= value` | Supersede; `default` follows the member name or completed value expression. |
| Duplicate member values | Retain; they compare equal and the first declaration supplies ordinary string formatting. |
| Omitted backing | Retain with `Integer` for strict/relaxed and `UInteger` for flags. |
| Enums inherit mathematical and bitwise operations | Reject for strict enums; use selective `= existing`. Relaxed enums reuse identity exposure, and flags receive only the closed bitwise bundle. |
| `enumValue as ExactBacking` | Retain as generated semantic projection. |
| `enumValue unsafe as OtherInteger` | Reject; project to the backing and then use ordinary integer conversion. |
| Direct `enumValue as OtherInteger` is rejected | Retain. |
| Two-step conversion through the backing type | Retain under ordinary integer conversion rules. |
| Compile-time enum metadata and enum-to-string | Replace the narrow conversion need with demand-generated functions; defer general reflection, direct traversal, and concept-dependent iterator mechanics explicitly. |
| `$EnumType` generic conversion example | Defer generic enum constraints and families to generic work. |

#### Deferred consequences and lasting ownership

| Concern | Destination or required destination | Constraint and activation pressure |
| --- | --- | --- |
| Complete selection and matching | [Raw selection input](../raw/selection.md) | Activation must preserve defined unnamed values, flags combinations, aliases, and catch-all pressure. |
| Direct traversal and iterator protocols | [Raw iteration input](../raw/iteration.md) | `each` may first support compiler-known enum traversal; true iterator values and iterable parameters wait for concepts or an equivalent constraint model. |
| General reflection and metadata | [Raw reflection input](../raw/reflection.md) | Preserve declaration names/order and aliases without claiming that a runtime value remembers which alias produced it. |
| General compile-time member expressions | [Raw compile-time-execution input](../raw/compile-time-execution.md) | Member values must remain definition-time constants until broader compile-time integration is accepted. |
| Generated enum and endian families | [Raw generic input](../raw/type-parameters-and-generics.md) | Do not invent generic syntax here; each generated result must obey the ordinary enum model. |
| Partial/open enum extension | [Raw partial-type input](../raw/partial-types.md) | Original bodies seal; future extension must reconsider member order, defaults, strings, masks, and source stability. |
| ABI, FFI, persistence, and foreign unknown values | [Raw interop input](../raw/interop.md) | Equal storage is not yet an ABI promise; explicit backing and admission remain visible. |
| Expansion of eligible identity backing | Identity/integer owner or indexed raw destination selected during disposition | Eligibility must be explicit and must not follow accidentally from integer storage or permit fat backing. |
| Fat identities or enums | [Raw owned-composition input](../raw/owned-composition.md) | Current enums add no storage; programmers compose an enum into another type. |
| Exact unsafe categorization | [Safety and analysis](../../language/safety-and-analysis.md) and enum owner | Raw adoption is a defined unsafe permission that bypasses ordinary admission, not merely an intent acknowledgement. |

A dedicated human-facing enum owner is likely necessary. It should own the
programmer model, declaration policies, members, defaults, admission,
reachability, generated operations, flags, strings, costs, and diagnostics.

Existing owners retain narrower concerns:

- [the operator catalog](../../language/operator-catalog.md) catalogs exact
  generated forms and protection;
- [identity types](../../language/identity-types.md) owns the reusable identity,
  exposure, backing-place, and admission vocabulary;
- [integers](../../language/integers.md) owns eligible integer roles,
  representation, and conversion after projection;
- [declarations and bindings](../../language/declarations-and-bindings.md) owns
  the non-value declaration route and ordinary direct/default initialization;
- [endianness](../../language/endianness.md) owns endian-specific semantics;
- [safety and analysis](../../language/safety-and-analysis.md) owns the reusable
  unsafe-permission model; and
- the legacy `enums.md` page should retire after its useful evidence
  is promoted or moved to explicit deferred owners.

### Superseded initial reconstruction

The following reconstruction is retained as review history. It predates the
aligned findings above and contains rejected alternatives, including exact-only
backing, invariant-based strict validity, masked complement, absent ordering,
and candidate syntax. It must not be used as the current working conclusion.

#### Review entry point

##### Candidate programmer model

An enum is a distinct value type with an exact integer backing type. A member
name such as `Fruit.Apple` denotes a value of `Fruit`; it is not an untyped
integer constant. Equal backing values denote equal runtime enum values even
when more than one member name declares that value.

The candidate model separates concerns that the legacy material sometimes
combines:

1. **Backing representation:** the exact intrinsic integer type stored by each
   value.
2. **Admitted value domain:** which backing values satisfy the enum's contract.
3. **Semantic projection:** what value `enumValue as UnderlyingType` means,
   which may differ from raw storage for a semantic enum such as an endian enum.
4. **Operation surface:** equality, ordering, bitwise operations, and other
   behavior do not follow merely from integer backing.

This produces at least three useful behavioral cases, using temporary working
labels rather than proposed source keywords:

| Case | Admitted values | Characteristic behavior |
| --- | --- | --- |
| Restricted ordinary enum | The distinct values declared by its members | A finite named domain; arbitrary backing values require validation |
| Restricted flags enum | Zero and combinations containing only the enum's allowed bits | Bitwise composition is closed over the admitted mask; combinations need not have member names |
| Backing-total enum | Every value of the backing integer type | Arbitrary backing values are valid; endian semantic families are the current example |

Flags and backing-total validity are separate facts. A flags enum may reject
unknown bits, while an endian enum admits every backing value without being a
flags enum.

The following examples show the candidate shape. Exact policy spelling, default
backing type, flags spelling, and safe-admission spelling remain under review.

```zax
Fruit :: enum I8 {
  Apple = 4
  Banana = -7
  Pear = -10
  Orange default = 42
  Pineapple
  Grapefruit = 4
}

favorite : Fruit = Fruit.Orange
another : Fruit = Fruit.Apple
sameValue : Boolean = another == Fruit.Grapefruit // true in the candidate model

raw : I8 = favorite underlying value
semantic : I8 = favorite as I8
direct : Fruit = 42 // error: an integer does not implicitly cross the enum boundary

// Illustrative safe restricted-enum admission; exact availability is unsettled.
maybeFruit : Fruit? = Fruit optional from raw
```

```zax
// Candidate flags policy; no source spelling for that policy is proposed yet.
Permission :: enum U8 {
  Read = 1
  Write = 2
  Execute = 4
  ReadWrite = 3
}

permissions := Permission.Read | Permission.Write
// The value is admitted even though Permission.ReadWrite could be omitted.
```

##### Most important contradiction

The current baseline says every enum has
`EnumType unsafe from rawValue`, that it performs no membership validation, and
that it may produce a backing value outside a restricted enum's named domain.
That cannot be combined casually with a closed enum model or exhaustive
selection:

```zax
Fruit :: enum U8 {
  Apple = 1
  Pear = 2
}

fruit := Fruit unsafe from (: U8 = 255)
```

Two coherent models are available:

1. **Validity is a type invariant.** `unsafe from` asserts that `255` satisfies
   `Fruit`'s domain. A false assertion has unsafe consequences; it does not
   create a defined third `Fruit` value. Selection may treat the distinct
   admitted member values as exhaustive.
2. **Every backing value remains a defined enum state.** Raw adoption creates a
   defined but unnamed `Fruit`. Named cases are never exhaustive unless the
   backing range is covered, and every generated or declared operation must
   define its behavior for unnamed values.

The candidate reconstruction favors the first model for a genuinely restricted
enum because it preserves a meaningful closed domain, useful exhaustiveness,
and optimizer-visible validity. It also exposes a naming problem: the current
safety model reserves `unsafe` for a narrow assertion or lower-level permission,
while an endian enum admits every backing value and therefore needs no
membership assertion. The review must decide whether the one spelling has a
type-specific safety reason, whether backing-total enums should also receive a
safe raw-adoption form, or whether the generated operation itself should be
revised.

This decision is prior to detailed selection syntax. Deferring it while claiming
both closed exhaustiveness and defined unknown values would leave the enum model
contradictory.

##### Decisions needing maintainer review

The most useful review order is:

1. Decide whether a restricted enum's admitted domain is an invariant or whether
   unnamed backing values are defined states.
2. Decide whether flags are a declared operation policy, a value-domain policy,
   or both; in particular, decide whether unknown bits are admitted.
3. Select a stable default exact backing type, or reject omission of the backing
   type. Do not silently use profile-selected `Integer`.
4. Decide whether duplicate-valued member names remain legal aliases and,
   consequently, whether selection exhaustiveness is over distinct values rather
   than declarations.
5. Decide the default-value rule for ordinary and flags enums.
6. Decide which operations are protected and generated: equality, ordering, and
   the flags bitwise set.
7. Confirm implicit member-value progression and its overflow behavior.

#### Recovered evidence and constraints

##### Legacy enum page

The legacy page supplies these candidate behaviors:

- `Name :: enum [UnderlyingType] { ... }` declaration shape;
- qualified member values such as `Fruit.Pineapple`;
- zero-based, incrementing implicit member values;
- continuation after an explicit value, so a member after `42` receives `43`;
- an explicit `default` marker, with the first member as the fallback default;
- duplicate member values;
- an optional explicit underlying integer type;
- bitwise combination and broad inherited numeric operations;
- compile-time enum metadata; and
- conversion to the exact underlying type, direct conversion restrictions, an
  `unsafe as` escape, and two-step conversion through the underlying type.

Only part of that evidence remains compatible with current owners. The current
operator and endian material accepts the four generated forms but explicitly
leaves general numeric inheritance, safe creation, validity, metadata, and
non-underlying conversion unresolved.

##### Current identity and integer pressure

Current identity design requires an explicit operation to cross a distinct type
boundary even when representation is identical. Integer backing does not make an
enum an integer or grant integer literal realization, arithmetic, or the
integer operator catalog.

The backing type is currently described as one exact fundamental intrinsic
integer. That matters for the omitted backing type:

- current `Integer` is a profile-selected identity rather than an exact
  fundamental type;
- inferring the narrowest backing type would make representation change when a
  member is added or edited; and
- an implementation-selected backing type would weaken portability and make
  `EnumType underlying type` unstable.

A fixed exact default such as `I32` would preserve the legacy omission form
while keeping representation predictable, but there is no reviewed evidence
selecting `I32`. Requiring the backing type would maximize explicit cost but
would reject the legacy shorthand. This remains a real design choice rather than
a documentation gap.

An unusual-width exact integer may back an ordinary enum. For an enum backed by
`I57`, `underlying type` is `I57` and `underlying value` returns an `I57`; neither
operation exposes the byte-rounded storage carrier or its non-value bits.

##### Current declaration and initialization pressure

`::` is the established family for non-value definitions, and current
declaration teaching already presents `Fruit :: enum { }`. Enum definition must
therefore remain a non-value declaration rather than a build-time value binding.

`name : EnumType = enumValue` is direct initialization. It does not first
default-initialize the enum and assign over it. `name : EnumType` invokes the
enum's zero-input/default initialization behavior. The enum review must define
that behavior rather than treating uninitialized storage as an implicit
possibility.

#### Detailed candidate reconstruction

##### Declaration and member values

The candidate retains this overall declaration shape:

```zax
Color :: enum U8 {
  Red
  Green
  Blue
}
```

The body belongs to `Color`'s defining authority and seals when the definition
closes. It may eventually contain owner-supplied functions as well as members,
but exact mixed body grammar and private access to an immediate backing place
remain unresolved. No enum declaration adds stored instance members or hidden
per-value storage.

Candidate member rules:

- Each member name declares a compile-time-known value of the enclosing enum
  type.
- `Color.Red` is a qualified path to that enum value.
- An explicit member value must be a compile-time-known integer expression
  exactly representable by the backing type.
- For an ordinary enum, the first omitted value is `0`; each later omitted value
  is the preceding member's backing value plus one, including after an explicit
  member value.
- Implicit progression that exceeds the backing range is a compile-time error;
  it never wraps.
- Member names are unique within the enum body.
- Duplicate backing values are candidate aliases, not distinct runtime tags.
  They compare equal and cannot be distinguished after the name expression has
  produced its value.

```zax
Status :: enum I8 {
  Ready = 1
  AlsoReady = 1
  Waiting
}

same := Status.Ready == Status.AlsoReady // true
nextBacking : I8 = Status.Waiting underlying value // 2
```

Allowing aliases preserves useful protocol and migration cases, but it creates
three consequences:

- selection cases for equal-valued names overlap;
- reflection must preserve all declared names without pretending that runtime
  values retain a source name; and
- enum-to-string behavior needs an explicitly chosen canonical name or an
  owner-supplied policy.

Disallowing aliases would remove those consequences but would intentionally
discard clear legacy behavior. The candidate keeps aliases for review rather
than assuming their acceptance.

Implicit values for flags need a separate decision. Incrementing `0, 1, 2, 3`
does not declare independent bits. Coherent choices include requiring every
flags member value explicitly or assigning omitted basis members successive
powers of two. If composite members and implicit values mix, "the next bit"
must be defined independently of the preceding composite value. The
reconstruction currently recommends requiring explicit values for flags until a
simple, stable rule demonstrates enough benefit.

##### Backing and admitted value domain

The backing type answers what bits can be represented. The admitted value domain
answers which represented values satisfy the enum's semantic contract. These
must not be treated as synonyms.

For a restricted ordinary enum, the candidate domain is the set of distinct
member backing values. Duplicate aliases do not enlarge it.

For a restricted flags enum with unsigned backing, define an allowed mask as the
bitwise OR of all declared member values. Zero and every submask of that allowed
mask are admitted. This admits unnamed compositions while rejecting unknown
bits:

```text
declared values: 1, 2, 4, 3
allowed mask:    7
admitted:        0 through 7
not admitted:    8, 9, ...
```

Requiring unsigned backing avoids a negative member implicitly making all high
bits part of the mask and keeps masked complement understandable. A future
review could deliberately permit signed flags, but ordinary signed-enum support
does not imply it.

A backing-total enum admits every value of its backing type. Endian enums need
this policy. Such an enum may still expose named values for convenience, but
those names cannot define an exhaustive domain.

An "unknown value" should mean one of two different things, and documentation
must not blur them:

- a valid admitted value without a declared name, such as an ordinary flags
  combination or any unnamed backing-total value; or
- a representable backing value outside a restricted enum's invariant.

The first is an ordinary enum value. Under the favored invariant model, the
second is not a defined enum value produced by safe execution. If the maintainer
instead chooses defined out-of-domain states, every operation and selection rule
below must be widened accordingly.

##### Default initialization

The legacy model designates one member as the default, using the first declared
member when no member carries `default`. That integrates cleanly with
`value : EnumType` and does not require the backing value zero to be admitted.

Candidate ordinary-enum rule:

- a restricted ordinary enum declares at least one member;
- at most one member declaration carries the default designation;
- with no explicit designation, the first member is the default;
- duplicate-valued aliases do not create multiple runtime defaults, but only
  one declaration may be designated; and
- `value : Fruit` constructs that default enum value.

```zax
Fruit :: enum I8 {
  Apple = 4
  Orange default = 42
}

fruit : Fruit // Fruit.Orange
```

Flags have a stronger neutral default: zero means that no flags are selected.
The candidate therefore makes zero admitted and default for flags, whether or
not a member names it. Permitting a nonzero explicit flags default is possible
but surprising because default construction would silently grant capabilities
or options. The reconstruction recommends rejecting a nonzero flags default
unless a concrete use case justifies it.

Backing-total enums need a declared semantic default independent of validity.
Endian integer families naturally default to semantic numeric zero. The enum
owner must teach whether backing-total user enums likewise default to zero or
select a member.

An empty restricted enum has no constructible value and no default. The
candidate rejects it rather than creating an impossible ordinary declaration.
Whether a backing-total generated enum can have no named members is independent.

##### Construction, admission, projection, and conversion

The four currently generated protected operations remain evidence:

| Form | Candidate meaning |
| --- | --- |
| `EnumType underlying type` | Return the exact default-qualified intrinsic backing type. |
| `enumValue underlying value` | Copy out the stored backing value unchanged, with the exact backing type. |
| `enumValue as UnderlyingType` | Project or decode the represented semantic value into the exact backing type. |
| `EnumType unsafe from rawValue` | Adopt exact backing storage without a runtime check, subject to the unresolved validity assertion. |

For an ordinary enum, semantic projection and raw extraction produce equal
integers even though they remain distinct operations. For an endian enum they
may differ because `as` decodes the represented number while `underlying value`
returns the stored byte arrangement.

Enum construction does not happen implicitly from an integer:

```zax
fruit : Fruit = Fruit.Apple
raw : I8 = 4

fruit = raw // error: no implicit crossing of the enum identity
```

The candidate admission surface follows the domain:

- A backing-total enum can offer ordinary destination-owned `from` because every
  semantic input value is admitted.
- A restricted enum can offer `optional from` to validate membership or allowed
  flag bits and return absence on rejection.
- A restricted enum may expose no safe arbitrary admission when its owner wants
  construction to occur only through members or declared domain functions.
- Raw adoption accepts only the exact backing type. It does not first perform an
  integer narrowing conversion.

```zax
maybeFruit : Fruit? = Fruit optional from raw
rawTooWide : U16
fruit := Fruit unsafe from rawTooWide
// error: rawTooWide is not Fruit's exact underlying type
```

The safe-admission examples are illustrative because the enum-specific
availability and exact phrase set are not accepted. They should reuse the
current identity admission vocabulary unless enum semantics demonstrate a real
need for another concept.

Direct `as` conversion is limited to the exact underlying type:

```zax
color : Color
raw : U8 = color as U8
wide : U16 = color as U16 // error: not the exact underlying type
wideViaBacking : U16 = color as U8 as U16
```

The second conversion uses ordinary integer rules, including exact, optional, or
narrowing forms as applicable. The legacy `unsafe as` shortcut from an enum to
an unrelated integer duplicates that composition and bypasses its explicit
range policy; the candidate does not retain it without a concrete use case.

Enums with equal backing types remain distinct:

```zax
fruit : Fruit
color : Color = fruit // error: unrelated enum identities
```

An explicit owner-declared bridge could be designed later, but representation
equality alone never creates one.

##### Equality, ordering, and flags operations

The candidate generates protected same-type equality and inequality for every
enum:

```zax
same : Boolean = leftFruit == rightFruit
different : Boolean = leftFruit != rightFruit
```

Equality compares represented enum values. For ordinary and flags enums this is
equivalent to backing-value equality. For endian enums, semantic equality and
raw equality also agree because endian encoding is one-to-one, even though
semantic projection and raw extraction return different integer values on an
opposite-endian execution environment.

No cross-enum or enum-to-integer equality is implied. Duplicate member aliases
compare equal.

Ordering is not inherent in declaration order, backing order, or integer
storage. These differ as soon as explicit or duplicate values appear:

```zax
Priority :: enum I8 {
  First = 10
  Second = -1
}
```

The candidate does not generate `<`, `<=`, `>`, or `>=` for ordinary enums by
default. An enum may deliberately expose an order based on semantic value or an
owner-supplied relation. If Zax instead generates backing-value ordering, the
documentation must call that numerical ordering rather than declaration order.

Restricted flags enums receive only the closed operations justified by their
policy:

- same-type `|`, `&`, and `^`, returning the same flags type;
- a complement operation, if retained, masked to the enum's allowed mask; and
- equality and inequality.

```zax
readWrite := Permission.Read | Permission.Write
withoutWrite := readWrite & ~Permission.Write
```

Raw integer complement would set every backing bit and violate a restricted
mask, so `~flag` cannot simply inherit the backing integer operation. A masked
complement has the useful enum meaning "all allowed flags not present." If that
meaning is judged too specialized, complement should be unavailable rather than
silently producing an out-of-domain value.

Arithmetic, shifts, rotations, reductions, signedness counterparts, and the
remaining integer catalog are not inherited. An enum owner may declare domain
operations under ordinary operator rules, but it cannot replace protected
generated signatures.

If unknown flag bits are admitted for forward compatibility, masked complement
and operation closure need a different contract. That is why "flags" alone
cannot decide the admitted value domain.

##### Exhaustive-selection constraint

Complete selection syntax remains deferred, but enum validity already constrains
its eventual model:

- Exhaustiveness for a restricted ordinary enum is over distinct admitted
  values, not member declarations. Alias cases overlap.
- A backing-total enum cannot be exhausted by listing a practical set of member
  names; it requires a catch-all or a range-capable future pattern.
- A flags enum is not exhausted by listing each basis flag because combinations
  are values too. It requires a flags-aware pattern model or a catch-all.
- A raw-adoption operation that creates defined unnamed ordinary-enum states
  would prevent member-only exhaustiveness.
- If raw adoption is instead an unsafe assertion of the enum invariant, the
  selection model may rely on that assertion just as other safe code relies on
  an unsafe lifetime or representation assertion.
- Future partial/open enum extension would separately affect source-stable
  exhaustiveness and remains deferred.

```zax
// Illustrative selection pressure; exact syntax is not decided.
switch fruit {
  case Fruit.Apple { }
  case Fruit.Pear { }
  // Exhaustive only if these are the distinct admitted values and raw
  // adoption cannot create a defined out-of-domain state.
}
```

```zax
// Illustrative flags pressure; individual names do not cover combinations.
switch permissions {
  case Permission.Read { }
  default { } // some catch-all remains necessary
}
```

These constraints have been checked against the live raw selection input. They
do not decide `switch`, `case`, patterns, guards, fallthrough, or transfer
behavior.

##### Endian semantic enums

Endian families demonstrate that enum identity, validity, semantic projection,
and operation surface are independent:

- they are distinct nominal enum types backed by an exact fundamental integer;
- every backing value is admitted;
- `underlying value` returns raw storage;
- `as UnderlyingType` decodes the represented number;
- destination-owned `from` encodes a native or other-endian semantic value;
- raw adoption preserves storage;
- they expose a focused closed operation surface; and
- they are not ordinary flags merely because bitwise operations are available.

The general enum owner should define the four shared operations and the relevant
validity policies. The endian owner should continue to define endian-specific
semantic conversion and operation behavior. Generated family names and the
mechanism producing the family remain generic/numeric work.

##### Representation, costs, and portability

An enum adds type identity but no per-value storage beyond its exact backing
integer. It has the backing type's instance size, alignment, and representation
extent, subject to future formal layout or ABI contracts.

Expected costs:

- member use, same-type equality, backing extraction, and ordinary same-bits
  projection can be representation-free operations;
- optional admission to a restricted ordinary enum may require one or more
  comparisons or a compiler-generated lookup strategy;
- restricted flags admission requires an unknown-bit mask check;
- endian semantic conversion may require byte rearrangement;
- no runtime reflection table, member-name table, or string conversion cost is
  implied merely by declaring an enum; and
- raw adoption performs no runtime validation, but its contract must say what
  the programmer is asserting.

Source stability pressures:

- inserting a member before implicitly valued members changes their values;
- changing a member can change a flags allowed mask and therefore admission;
- inferring the backing type from member values would make layout and generated
  signatures unstable;
- changing the default member changes behavior of every `value : EnumType`;
- adding an alias creates overlapping selection names and affects reflection or
  formatting policy;
- adding a member to a restricted closed enum changes exhaustiveness; and
- adding a protected generated operation can make formerly invalid source valid
  or conflict with an owner declaration.

Programmers should use explicit backing and member values when values cross
persistence, protocol, or foreign boundaries. Exact ABI/FFI guarantees remain
future interop work.

##### Diagnostics that the eventual owner should make predictable

The candidate model needs clear diagnostics for:

- a member value not representable by the exact backing type;
- implicit progression beyond the backing range;
- duplicate member names;
- duplicate backing values if aliases are ultimately rejected;
- more than one explicit default member;
- an empty restricted enum;
- a nonzero flags default if that candidate restriction is accepted;
- signed backing for a restricted flags enum if unsigned backing is required;
- a flags member or operation that violates the allowed-mask rule;
- implicit integer-to-enum construction;
- direct conversion to a non-underlying integer type;
- raw adoption from a value that is not the exact backing type;
- arithmetic, ordering, shift, or bitwise operations not in the enum's declared
  surface;
- equality or assignment across distinct enum identities;
- overlapping selection cases caused by aliases; and
- non-exhaustive selection when unnamed admitted values remain possible.

#### Legacy disposition candidates

| Legacy evidence | Candidate disposition |
| --- | --- |
| `Name :: enum [UnderlyingType] { ... }` | Retain the overall non-value declaration shape; settle exact body/policy grammar here. |
| Implicit values begin at zero and increment | Retain for ordinary enums, with exact range checking and no wrap. |
| Member after explicit `42` becomes `43` | Retain for ordinary enums. |
| First member is default unless another has `default` | Retain as the ordinary-enum candidate; use zero for flags. |
| Duplicate values are allowed | Retain as alias candidate, with equality, selection, reflection, and formatting consequences explicit. |
| Underlying type may be omitted | Retain only if a fixed exact default is selected; do not infer or silently use profile `Integer`. |
| Enums inherit mathematical and bitwise operations | Reject as a general rule. Generate equality and policy-specific flags operations only; require deliberate owner behavior otherwise. |
| `fruit as ExactUnderlying` | Retain as the generated semantic projection. |
| `fruit unsafe as OtherInteger` | Do not retain without separate casting pressure; explicit projection followed by integer conversion is coherent. |
| Direct `fruit as OtherInteger` is rejected | Retain. |
| Two-step conversion through the backing type | Retain, governed by ordinary integer conversion rules. |
| Compile-time enum metadata and enum-to-string | Defer to reflection with alias and canonical-name pressure preserved. |
| `$EnumType` generic example | Defer to generic/type-parameter work; it supplies no current enum syntax. |

#### Known holes and risks

- **Raw-adoption semantics are blocking.** The design cannot promise both a
  closed invariant and a defined out-of-domain state without explaining which
  claim selection and optimization may trust.
- **The default backing type is unsupported by evidence.** `I32` is a plausible
  candidate, not a recovered decision.
- **Flags policy needs source and semantic separation.** Bitwise availability,
  admitted combinations, unknown-bit handling, and default value are related but
  not identical.
- **Aliases erase source-name identity at runtime.** Any reflection or formatting
  design that assumes one name per value will fail.
- **Ordering has no obvious universal meaning.** Generating it from backing
  representation would be easy but may encode accidental member numbers as
  domain order.
- **Mixed enum bodies need grammar.** Member declarations and owner-supplied
  functions must remain distinguishable without inventing a second namespace or
  silently treating members as ordinary stored declarations.
- **Semantic enums generalize beyond ordinary projection.** The shared enum model
  must not define `as UnderlyingType` as raw extraction, because endian enums
  already demonstrate otherwise.
- **Flags complement can violate validity.** It must be masked or unavailable.
- **Exhaustiveness and future extension interact.** This work can constrain
  finite closed selection without deciding whether enums are later extensible.

#### Adjacent findings deliberately deferred

| Deferred concern | Live destination | Activation pressure | Constraint on this work |
| --- | --- | --- | --- |
| Complete selection and pattern syntax | [Raw selection input](../raw/selection.md) | A selection-focused work item | Preserve distinct-value exhaustiveness, alias overlap, unnamed flags combinations, backing-total values, and raw-adoption consequences. |
| Member iteration, declared names, canonical formatting, and enum-to-string | [Raw reflection input](../raw/reflection.md) | Reflection or generated metadata review | Do not claim that a runtime value retains which alias spelling produced it; preserve all declarations as metadata candidates. |
| Generic enum families and generated endian names | [Raw generic input](../raw/type-parameters-and-generics.md) | A proposal requiring enum family generation | Ordinary enum semantics must be sufficient to describe each generated result type without accepting a generic mechanism here. |
| Partial/open enum extension | [Raw partial-type input](../raw/partial-types.md) | A proposal to add members outside the original definition | Do not make extension assumptions part of closed-enum exhaustiveness. |
| ABI, FFI, and external unknown-value preservation | [Raw interop input](../raw/interop.md) | A concrete foreign or persistent representation contract | Keep exact backing and explicit admission visible; do not promise ABI from equal storage alone. |
| Exact unsafe category and raw-adoption classification | [Safety and analysis](../../language/safety-and-analysis.md) plus the enum owner | Resolution of restricted validity and endian raw adoption | `unsafe` must identify a real assertion or permission; it cannot remain only a vague synonym for "no check." |
| Enum body reflection and private immediate backing access | Retired raw enum input pending an enum owner | Acceptance of mixed enum bodies or owner-supplied low-level behavior | Do not expose `underlying place` publicly or let body behavior add per-value storage. |

#### Likely lasting ownership

This is a candidate ownership map, not a promotion proposal or edit
authorization:

- A dedicated human-facing enum owner is likely necessary because declaration,
  members, validity, defaults, admission, operations, costs, and selection
  pressure form one programmer concept that would otherwise be reconstructed
  across unrelated documents.
- The enum owner should own the four shared operations' enum semantics.
  [The operator catalog](../../language/operator-catalog.md) should continue to
  catalog their exact forms and protection.
- [Zax endianness](../../language/endianness.md) should continue to own semantic
  encode/decode, receiver correctness, and the focused endian operation surface.
- [Zax integers](../../language/integers.md) should continue to own backing
  eligibility, unusual-width behavior, and integer conversion after projection.
- [Zax identity types](../../language/identity-types.md) should own reusable
  identity-boundary and admission vocabulary without becoming a duplicate enum
  definition.
- [Zax declarations and bindings](../../language/declarations-and-bindings.md)
  should retain the non-value declaration route and generic direct/default
  initialization behavior, with a concise handoff to the enum owner.
- The legacy `enums.md` page should be consumed by promotion: useful
  behavior moves to current owners, still-unresolved evidence moves to a live raw
  destination, and superseded teaching does not remain as a competing public
  model.

## Dispositions and promotion dry run

### Dry run 2026-09-07: PASS

The aligned enum semantics have a coherent dedicated owner, local handoffs, and
deferred destinations. The language maintainer resolved the two source-level
integration questions found by the initial dry run:

1. `final once` provides the narrow general type-callable function contract
   needed by generated enum functions and their replacement states.
2. `case-conflicting-enum-member-names` is the exact intent category, applied
   around the complete enum declaration.

The traversal dependency does not itself cause failure. Enum behavior is
coherent when declaration order, matching, and atomic conversion requirements
are preserved without choosing `each` syntax or a first-class iterator protocol.

No lasting owner was edited by this dry run. The result is **PASS**: the
findings have a coherent human-facing structure, unique ownership, live
deferred destinations, and an exact promotion set. PASS does not itself
authorize promotion; the language maintainer separately authorized immediate
promotion when this result became PASS.

#### Resolved source decisions

##### Type-callable generated functions

The aligned call surface includes:

```zax
maybeColor := Color.fromString(text)
maybeColor := Color.fromStringIgnoringCase(text)
maybeName := color.toString()
```

The maintainer aligned the narrow general rule:

- a `once` function inside a type has one type-owned implementation shared by
  all instances;
- it is callable through either `Type.function(...)` or
  `instance.function(...)`;
- `_` has the `Nothing` instance state on a type call and identifies the
  instance on an instance call;
- `final` prevents reassignment under ordinary function rules; and
- generated functions participate in the ordinary omitted, `= default`, body,
  and `= forbidden` declaration states.

Global and `once` value initialization, concurrency, teardown, capture, and
generic-specialization behavior remain deferred. Promotion updates the general
declaration and invocation owners with only this callable rule and narrows the
raw `once` input accordingly.

##### Intent category for case-conflicting names

The aligned semantic rule is complete:

- ASCII case-equivalent names with the same enum value need no acknowledgement;
- case-equivalent names with different values produce an
  acknowledgement-required intent error;
- acknowledgement permits the declarations but unique case-insensitive lookup
  still returns absence for the ambiguous folded name; and
- the multi-value lookup yields the distinct matches.

The maintainer accepted
`intent<case-conflicting-enum-member-names>{...}` around the complete enum
declaration. The category applies to strict, relaxed, and flags enums. The
acknowledgement changes no member value and does not choose a unique
case-insensitive result.

#### Proposed human-facing structure

Create `language/enums.md` as the dedicated cohesive concept owner. A cold
reader should not have to reconstruct enum behavior across identity, integer,
operator, endian, and raw-input documents.

The document should teach in this order:

1. strict enum declaration, known members, qualification, and default use;
2. enum-as-specialized-identity, backing choice, and explicit boundary crossing;
3. member values, aliases, member prologue, functions, and the no-stored-members
   rule;
4. ordinary admission versus safely reachable and representable unnamed values;
5. generated comparison and selectively requested `= existing` behavior;
6. `relaxed` enums;
7. `flags` enums, allowed-mask admission, defaults, and the closed bitwise
   surface;
8. underlying type/value/place, semantic projection, safe admission, and unsafe
   raw adoption;
9. exact/case-insensitive string conversion and its ambiguity rules;
10. deferred member/match traversal and flags multi-string admission;
11. exhaustive-selection pressure;
12. endian semantic enum handoff;
13. costs, diagnostics, source stability, and reference details.

The opening should use a small strict enum before introducing admission-domain
terminology. Relaxed, flags, unsafe, alias ambiguity, iteration, and endianness
belong after the ordinary model they vary.

#### Ownership map

| Concern | Lasting owner | Local integration |
| --- | --- | --- |
| Enum mental model; strict, relaxed, and flags policies | Proposed `language/enums.md` | `index.md` adds the current owner and removes the legacy route |
| Known members, aliases, defaults, body transition, and no stored members | Proposed enum owner | Declarations routes the non-value form; source structure owns the shared newline/continuation and ASCII-identifier rules |
| Eligible integer backing and profile-selected representation | [Integers](../../language/integers.md) for integer facts; proposed enum owner for enum eligibility | Identity types explains the nested boundary when a language-provided integer role backs an enum |
| Specialized-identity behavior, exposure, and private immediate backing place | [Identity types](../../language/identity-types.md) for the reusable mechanism; proposed enum owner for enum policy | [Operator catalog](../../language/operator-catalog.md) catalogs the generated forms |
| Safe admission, reachable unnamed values, unsafe raw adoption | Proposed enum owner for domain behavior | [Safety and analysis](../../language/safety-and-analysis.md) owns the reusable unsafe-permission distinction |
| Comparisons, relaxed exposure, selective `= existing`, flags mask and operators | Proposed enum owner | Existing operator and `= existing` owners retain general selection/reuse rules |
| Generated enum/string functions | Proposed enum owner | Declaration/invocation owners define the aligned narrow `once` callable rule |
| Case-conflicting enum-name acknowledgement | Proposed enum owner for the conflict behavior | [Intent acknowledgements](../../language/intent-acknowledgements.md) owns the exact category registry and enclosure meaning |
| Endian encode/decode and focused operation surface | [Endianness](../../language/endianness.md) | Proposed enum owner teaches why semantic projection may differ from raw extraction |
| Selection syntax and catch-all behavior | [Raw selection input](../raw/selection.md) | Proposed enum owner states only the enum facts selection must preserve |
| `each` and compiler-known enum traversal | Proposed indexed raw `iteration.md` | Proposed enum owner preserves semantic result/order requirements without assuming first-class iterator values |
| Concepts-based iterator and iterable protocols | [Raw generic input](../raw/type-parameters-and-generics.md) and proposed raw `iteration.md` | Direct `each` work must not invent generic constraints implicitly |
| General enum declaration metadata and reflection | [Raw reflection input](../raw/reflection.md) | Generated strings and narrow traversal requirements do not establish general reflection |
| General compile-time-produced member constants | [Raw compile-time-execution input](../raw/compile-time-execution.md) | Proposed enum owner accepts only definition-time integer constants under the current boundary |
| Generated enum families and endian-family names | [Raw generic input](../raw/type-parameters-and-generics.md) | Each produced type must obey the ordinary enum model |
| Partial/open enum extension | [Raw partial-type input](../raw/partial-types.md) | Original enum bodies seal; future work must preserve member order, defaults, masks, and string behavior |
| Enum ABI/FFI and foreign unknown values | [Raw interop input](../raw/interop.md) | Equal storage is not yet a foreign contract |
| Fat identity/enum representation | [Raw owned-composition input](../raw/owned-composition.md) | Current enums add no storage; programmers compose a fat type |
| Session keep-alive `ping` response | [New-session handoff source](../handoff.md) and [same-session rehydration source](../rehydrate.md) | Mirrored project guidance; not language behavior |

#### Structure and route proposal

- Create `language/enums.md`; create no new language directory.
- Add the enum owner to the current-concept list in `index.md`.
- Remove the legacy enum entry from that index.
- Retire root `enums.md` after all useful language content is in the new owner or
  a live deferred destination.
- Retire `project/raw/enum-types.md` after its retained questions are promoted or
  distributed to the indexed raw owners below.
- Create `project/raw/iteration.md` and add it to
  `project/raw/README.md`. It should combine the already indexed `each in` /
  `each from` pressure with compiler-known enum member/match traversal and
  multi-string flags input. It must keep true iterator values and general
  iterable parameters deferred until concepts can express their contracts, and
  require future work to return to the enum owner with concrete declarations and
  examples.
- Update raw files that currently point to enum raw input so they point to the
  current enum owner for accepted facts and retain only their independently
  unresolved concern.
- Update the active work record's reading-scope and retained-history links when
  deleting the legacy and raw enum files so the live tree has no broken links.
  Historical provenance remains in this work record and Git history.
- Keep `project/README.md` unchanged during promotion. Its active pointer changes
  only during separately authorized closure and continuation.
- Add the same keep-alive rule to both operating-prompt sources:

  > If the language maintainer sends `ping` as the complete message, respond
  > with only `pong`. Do not analyze, use tools, or add other text for that
  > message.

  The two sources must use equivalent wording and retain their existing generic
  active-work placeholders.

Raw and project material remains outside ordinary public language navigation.
The website route reaches the proposed enum owner through `index.md`; no public
route points to active work or raw iteration input.

#### Proposed exact promotion change set

This is the complete promotion set authorized after the PASS result.

Create:

1. `language/enums.md` - cohesive current conceptual enum owner.
2. `project/raw/iteration.md` - indexed direct-`each`, compiler-known traversal,
   and later concept-dependent iterator pressure.

Update current language owners and routers:

3. `index.md` - add current enums and remove the legacy enum route.
4. `language/declarations-and-bindings.md` - route `:: enum` and enum default
   initialization to the enum owner without duplicating enum semantics.
5. `language/source-structure.md` - record ASCII identifier naming and hand off
   enum-local member-prologue grammar rather than duplicating it.
6. `language/identity-types.md` - identify enums as specialized identities,
   preserve nested identity boundaries for integer-role backing, and hand off
   enum policy.
7. `language/integers.md` - replace exact-only enum backing statements with
   aligned intrinsic/language-role eligibility and unsigned flags constraints.
8. `language/operator-catalog.md` - route generated enum forms to the new owner,
   add private enum `underlying place`, and replace the resolved raw-adoption
   classification deferral.
9. `language/endianness.md` - link to the current enum owner, preserve direct
   intrinsic backing and semantic projection, and remove resolved general-enum
   deferrals.
10. `language/safety-and-analysis.md` - hand off to the enum owner and recognize
    out-of-admission `unsafe from` as a defined domain permission rather than a
    false assertion.
11. `language/intent-acknowledgements.md` - add
    `case-conflicting-enum-member-names`.
12. `language/function-invocation.md` and
    `language/declarations-and-bindings.md` - define the narrow type-callable
    `once` behavior needed by generated enum calls and replacement.
13. `language/terms.md` - correct type-result examples to use type aliases.
14. `casting.md` - replace the retired enum-conversion deferral with the current
    projection, two-step conversion, and raw-adoption model.

Update and create live deferred destinations:

15. `project/raw/README.md` - remove consumed enum input and index iteration.
16. `project/raw/selection.md` - add defined unnamed values, flags combinations,
    aliases, and catch-all pressure.
17. `project/raw/reflection.md` - point accepted enum facts to the current owner,
    correct its `type of` alias example,
    and retain only general metadata/reflection pressure.
18. `project/raw/compile-time-execution.md` - preserve broader constant-expression
    eligibility required by future enum member expressions.
19. `project/raw/type-parameters-and-generics.md` - point generated family
    constraints to the current enum owner.
20. `project/raw/partial-types.md` - preserve enum extension consequences for
    member order, defaults, admission, masks, strings, and body sealing.
21. `project/raw/interop.md` - preserve enum layout, foreign unknown-value, and
    admission/adapter pressure.
22. `project/raw/owned-composition.md` - preserve the future fat-identity pressure
    while recording that current enums add no storage.
23. `project/raw/numeric-type-families.md` - replace obsolete exact-only enum
    statements and raw-enum links with the current owner boundary.
24. `project/raw/feature-catalog.md` - route enum features to the current owner
    and `each` iteration to the new raw iteration input.
25. `project/raw/global-and-once-lifetimes.md` - narrow the unresolved `once`
    dependency to value initialization, concurrency, teardown, capture, and
    generic-specialization behavior.

Update project-process sources and the active record:

26. `project/handoff.md` - add the complete-message `ping` / only-`pong` rule.
27. `project/rehydrate.md` - add the synchronized rule.
28. `project/work/016-enum-types-and-values.md` - record final dispositions and
    repair links to retired inputs.

Retire consumed inputs:

29. `enums.md`.
30. `project/raw/enum-types.md`.

No archive move, work-item `017`, project current-work pointer, staging,
committing, or pushing belongs to this promotion set.

#### Cold-reader and completeness result

The proposed dedicated owner passes the conceptual teaching test:

- ordinary source and member use can lead;
- specialized identity and admission terms gain meaning through concrete
  consequences;
- strict, relaxed, and flags are organized by the policy that varies;
- unsafe, endianness, iteration, and exhaustive selection follow their
  prerequisites; and
- reference detail remains available without opening with an audit ledger.

Every aligned semantic finding has a current owner, local handoff, or proposed
indexed raw destination. Legacy enum and enum-raw content can retire without
loss once the proposed set is applied.

The dry run is **PASS**. The aligned findings can be promoted through the exact
change set above without duplicate authority, orphaned material, or reliance on
archived work. The language maintainer separately authorized immediate
promotion after this PASS result.

### Promotion result 2026-09-07

Promotion completed through the authorized PASS change set.

- `language/enums.md` is the cohesive current conceptual owner.
- Current declaration, invocation, source, identity, integer, operator, endian,
  safety, and intent owners contain their local enum and narrow `once`
  integration.
- `project/raw/iteration.md` is the indexed live destination for direct `each`,
  compiler-known enum traversal, multi-string flags input, and later
  concept-dependent iterator pressure.
- Selection, reflection, compile-time execution, generics, partial extension,
  interop, owned composition, numeric-family, feature-catalog, and remaining
  `once` input retain only their independently unresolved concerns.
- Legacy `enums.md` and consumed `project/raw/enum-types.md` were retired after
  their useful content was promoted or dispositioned.
- `casting.md` was added to the promotion set during validation because it
  contained a live link to the retired enum input and stale deferred conversion
  claims. Its enum note now routes to current behavior.
- Post-promotion review replaced a misleading anonymous zero-storage member with
  a named nested type, clarified the no-fat-representation rule, and corrected
  type-producing examples to use `:: alias type`, including the cross-cutting
  terminology owner.
- Post-promotion review also removed the premature assumption that enum
  traversal must return first-class iterators. Direct `each` over compiler-known
  sources may precede concept-dependent iterator protocols.
- `project/handoff.md` and `project/rehydrate.md` contain the synchronized
  complete-message `ping` / only-`pong` rule requested by the language
  maintainer.

Validation completed:

- `git diff --check` passes;
- all links and heading anchors in 88 live Markdown files resolve when
  `project/archive/` is excluded as required;
- public navigation reaches the current enum owner and no longer routes through
  the retired legacy enum page;
- no live link depends on either retired enum input;
- the prompt-source rule is textually synchronized; and
- the language maintainer staged the complete 30-file promotion and review
  corrections before closure.

### Closure result 2026-09-07

The language maintainer approved the promoted enum design and post-promotion
review corrections. Work item `016` closed with:

- current enum behavior owned by `language/enums.md` and its integrated owners;
- direct `each` and compiler-known traversal prepared as work item `017`;
- first-class iterator protocols explicitly deferred until concepts can express
  their constraints;
- every remaining enum consequence assigned to a current owner or indexed raw
  destination; and
- final validation passing before archival.

The work item moved to `project/archive/work/016-enum-types-and-values.md`,
`project/README.md` now identifies `017` as active and `018` as next, and the
archive index records this outcome. The creating agent does not analyze `017`.
