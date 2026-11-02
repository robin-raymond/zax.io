# 031: Casting

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `031` |
| Created | 2026-09-23 |
| Completed | 2026-09-24 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted conversions-and-casts, pointer, composition, Nothing-instance, invocation, operator, qualifier, integer, and terms owners |

## Non-authority notice

This file is a historical audit record. It is non-authoritative and excluded
from ordinary onboarding. Current language meaning lives in the promoted
`language/` owners rather than this discussion history. Links to the retired
root `casting.md` and the retired raw casting notes are historical.

## Fixed initiating input

This section records the aligned information known when work item `031` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Mine legacy [`casting.md`](../../casting.md) by value so that page can receive
a complete disposition.

Casting here includes pointer casts and `as` operator casting. It is not limited
to either one. Current documentation already teaches substantial `as`,
conversion, admission, and recast behavior. The review distinguishes teaching
that is already current from legacy material that is still missing, and from
legacy material the maintainer notes mark defunct or superseded.

The maintainer-supplied
[casting notes](../raw/casting-maintainer-notes.md) are the primary input for
how casting should work. They may add to, replace, supersede, or reject
conflicting legacy evidence, and they may introduce new casting ideas. The
notes are empty at the creation of this work item. The maintainer will write
them before the mining review is assigned. Emptiness is not a finding and is
not permission to treat legacy `casting.md` as the preferred model.

For each distinct concept, rule, source form, example, or design pressure:

- determine whether current documentation already teaches its useful meaning;
- identify worthwhile material that current owners do not yet preserve;
- identify conflict with, or supersession by, current conceptual design or the
  maintainer notes;
- explain ambiguity that prevents a responsible disposition;
- recommend the lasting owner or explicit deferral for material worth keeping;
  and
- recommend whether the legacy page can be deleted after all useful input is
  promoted, deferred, rejected, or superseded.

The review must do more than recommend deletion. Any concept worth preserving
must be made understandable in plain language, supported by representative Zax
source where source can clarify it, and shaped like material that could later be
promoted for human developers.

### Review posture

- Treat `casting.md` as legacy evidence, not accepted design.
- Treat the maintainer notes, once written, as the preferred direction unless
  concrete semantic, cross-feature, teachability, or feasibility pressure
  exposes a flaw. Legacy precedence by itself is not such a flaw.
- Treat current `language/` owners as current for the concerns they own.
- Do not preserve legacy syntax merely because it exists.
- Do not promote rejected or superseded concepts merely to deny them in current
  teaching.
- When useful intent survives but its old syntax or mechanism does not,
  separate the intent from that obsolete vehicle.
- Distinguish duplicate current teaching from genuinely missing language
  design.
- Follow material consequences far enough to expose conflicts among pointer
  casts, `as`, identity, structure, and qualification without redesigning
  unrelated language areas.
- If no unique useful material remains after the audit, recommend deleting
  `casting.md`.

### Initial stopping guidance

Do not begin the mining review while
[casting notes](../raw/casting-maintainer-notes.md) are still empty. Stop and
wait for the maintainer to write them.

Creating and routing this work item does not authorize analysis. The session
that creates `031` stops before that review. A later assignment begins it.

Do not promote findings, delete `casting.md`, archive this work item, or create
work item `032` without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, legacy consumption, promotion, deferral, and closure.
- [Casting maintainer notes](../raw/casting-maintainer-notes.md) - primary
  account of current casting intent, including what is defunct, superseded,
  still valid, or new. Read this before mining the legacy page. If it is still
  empty, stop.
- [Legacy casting](../../casting.md) - immutable source corpus to mine and
  disposition after the notes are present.

### Consequence-driven

Read only the smallest relevant sections after a concrete note or legacy claim
crosses one of these boundaries:

- [Zax integers](../../language/integers.md) and the
  [integer operator catalog](../../language/integer-operator-catalog.md) when
  integer `as`, narrowing, optional conversion, or admission is implicated.
- [Zax identity types](../../language/identity-types.md) when identity
  admission or projection is implicated.
- [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  when structural recast, same-storage views, or `unsafe cast` is implicated.
- [Zax composition](../../language/composition.md) when outer cast is
  implicated.
- [Strings and characters](../../language/strings-and-characters.md) when
  string-identity conversion is implicated.
- [Operator catalog](../../language/operator-catalog.md) when the spelled `as`
  forms are implicated.
- [Pointers, allocation, and arenas](../../language/pointers-and-arenas.md) and
  [raw pointer and arena mechanics](../raw/pointer-and-arena-mechanics.md) when
  a pointer cast is concrete.
- [Zax qualifiers](../../language/qualifiers.md) when a cast changes access,
  mutability, or replacement authority.

### Audit-only

- Archived numbered work only when a concrete provenance or regression question
  cannot be answered from the maintainer notes, `casting.md`, and the current
  owners named above.

## Working record

### Record status

- **2026-09-23, initial reconstruction.** Candidate analysis written under the
  new-session working-record authorization.
- **2026-09-23, three review rounds.** Maintainer feedback and alignment. The
  record was then rewritten under explicit maintainer authorization.
- **2026-09-23, fourth and fifth review rounds.** These covered `unsafe cast`
  pointer roles, `IPointer` retirement, role-preserving `outer cast`, the
  replacement intent, and the anti-scenario rule. They were recorded under
  explicit maintainer authorization.

This record now holds **aligned findings** (not yet promoted), a small number of
**open items**, **captured deferrals**, and a short list of **withdrawn or
rejected** material. Initial-reconstruction candidates that review superseded
were removed rather than kept beside the aligned result. The staged maintainer
notes remain the raw source of intent.

Reading beyond the required set was consequence-driven:

- operator catalog: protected domains, conversion and admission, outer cast,
  transfer stances, lifecycle, and reserved phrase forms;
- structural shapes: view-shaped `unsafe cast`;
- pointers and arenas: raw pointers, ownership roles, transitions, opaque
  types, and anchored interior pointers;
- Nothing instances: dereference and invalid writes;
- qualifiers: strengthening, unsafe cast, and `unsafe pliable`;
- safety and analysis: selected contracts;
- integers: pointer-representation roles;
- floating-point scalars: numeric conversion;
- operator phrases: type slots, stance phrases, keyword words, reserved forms;
- raw pointer mechanics and raw generics: relevant sections;
- `basics.md`: the legacy historical operator list.

### How to read this record for promotion

Each aligned finding starts with the **teaching shape**: the model and source a
programmer should meet first. The **rules** underneath list what promotion must
preserve. Promotion reconstructs teaching from these; it does not paste them.
Several findings deliberately say what *not* to teach, where a promoted owner
would otherwise be tempted to deny a retired form.

**Anti-scenario rule (maintainer reaffirmed; already stated in
`documentation.md`).** A promoted owner shows an invalid form only when a
programmer might reasonably think it viable. For example, a bare by-value
`unsafe cast`, or plain `car.engine` used as ownership. Retired forms that no
current reader has seen are removed silently and never taught, even to deny
them. Examples are `as default`, disabling `as`, legacy `outer of`, `IPointer`,
and `anchored by`.

---

## Aligned findings

### A1. There is no general "convert anything" operation

**Teaching shape.** A programmer coming from other languages expects a cast
operator that converts any value to any type. Zax has none. What other languages
call "casting" is several distinct operations, each with its own meaning and
cost:

```zax
display := boiling as Fahrenheit              // a conversion the type chose to provide
myByte := myWide narrowing as U8              // language-provided numeric conversion
moved := source as move                       // restate a transfer stance; converts nothing
view := source as layout MyLayout             // structural compatibility
raw := myPointer unsafe cast OtherType *      // unchecked reinterpretation
car : Car & = engine outer cast Car.engine    // member back to its container
```

The casting document opens with a short guided tour: one small example per
family, a sentence on what it means, and a handoff to the owner that teaches it
fully. `unsafe cast` is the exception and is taught completely in the casting
document (A4).

**Rules to preserve:**

- `as` is an ordinary operator phrase word. Most `as` operations are ordinary
  receiver-owned operators. The language is special only in a narrow set of
  protected forms and protected intrinsic signatures (A8).
- The language provides no built-in generic conversion. Coercive
  structural conversion already covers memory-compatible reinterpretation with
  a validity story. Anything else is a type's own `as` operator or a named
  protected operation.

**Family map for the tour** (owners stay authoritative):

| Family | Tour example | Owner |
| --- | --- | --- |
| Custom conversion | `boiling as Fahrenheit` | casting document (A2) |
| Numeric conversion | `myWide as Binary32`, `myRuntime as Binary32?`, `narrowing as` | integers, fixed-point, floating-point |
| Identity and enum admission/projection | `MyIdentity from mySource`, `myEnum as UnderlyingType` | identity types, enums |
| Transfer-stance restatement | `source as move` | transfer stances |
| Structural compatibility and coercion | `source as layout Destination`, `source unsafe as coercive layout Destination &` | structural shapes |
| Unchecked reinterpretation | `pointer unsafe cast Destination *` | **casting document** (A4) |
| Pointer ↔ integer | `myPointer as UPointer`, `myAddress unsafe cast MyType *` | casting document for the forms (A5); integers for the capacity roles |
| Container recovery | `engine outer cast Car.engine` | composition |
| Interior ownership | `inner container.item` | pointers (A7) |
| Pointer/reference formation and dereference | `pointer : * = value`, `view : MyValue & = pointer.` | declarations, Nothing instances, pointers (A6) |

### A2. Custom conversion is an ordinary `as` operator

**Teaching shape.** A type provides a conversion by declaring binary `as` with
a type parameter slot:

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
    result.degrees = degrees * 9.0 / 5.0 + 32.0
    return result
  }
}

boiling : Celsius = [{ degrees: 100.0 }]
display := boiling as Fahrenheit
```

`DestinationType` is a type argument. It has no runtime storage, lifetime, or
evaluation.

**Rules to preserve:**

- The receiver owns the conversion: the left value's type supplies discovery.
- **One declaration is written for one intended destination type.** No current
  mechanism restricts which destinations the declaration accepts. Generics will
  fix this; for now the teaching may state this limitation plainly and move on,
  without inventing constraint syntax.
- **There is no built-in `as MyType *` or `as *`.** Pointer formation is
  implicit (A6). A programmer may still declare a custom `as` whose destination
  is a pointer or reference type. Its result then follows the ordinary
  returned-reference and receiver-origin lifetime rules.
- **Programmer phrases that begin with `as` are allowed but discouraged**, for
  example `'as json text'`. A future protected `as` word could collide with one.
  A lint discourages them. The language will never protect binary `as Type`
  itself, except as protected intrinsic signatures on language-owned types.
- **`unsafe as` is an ordinary programmer phrase.** Inside a phrase, `unsafe` is
  a phrase word, not the keyword. A programmer may choose it to signal that their
  operation is risky. It grants no unsafe authority, names no analysis category,
  and receives no lint. The teaching should state that the language's own
  unchecked reinterpretation is `unsafe cast` (A4).
- **Scalars.** Integers, fixed-point, and floating-point conversions are
  protected intrinsic signatures supplied by the language. Programmer types interact with
  them this way:

  ```zax
  myCount := myTally as I32        // MyTally declares its own 'as': ordinary
  myTally := MyTally from myInt    // MyTally declares its own admission: ordinary
  myTally := myInt as MyTally      // needs a future partial route onto the integer domain
  ```

  Only the last direction, a scalar receiver producing a programmer type, lacks
  a route today (see deferrals).

**Correction to the previous round.** The chat described `myInt as MyType` as
"user type into scalar". It is scalar into user type. The substance aligned
("basically, yes") is unchanged.

**Not to teach:** a mechanism for disabling an `as` operator. Nothing defaults,
so nothing needs disabling.

### A3. Transfer-stance restatement is a protected form

`as copy`, `as deep`, `as move`, and `as last` are **protected forms**. No
programmer declaration can claim them for any receiver. They restate a stance
and convert nothing. Transfer stances remains their owner. The casting document
shows one example so that readers do not mistake them for conversions:

```zax
converted := source as DestinationType   // conversion
moved := source as move                  // stance restatement; no conversion, no transfer yet
```

`as default` is **retired**. Neither its type-identity shape nor its value shape
survives. Promoted owners remove it rather than teaching its absence.

### A4. `unsafe cast` is the language's unchecked reinterpretation

The casting document owns `unsafe cast` completely. Structural shapes keeps its
structural applications and the coercive forms, and links here.

**Teaching shape.** `unsafe cast` asks the compiler to treat memory as a
different type without converting or checking anything. The programmer takes
responsibility for layout, alignment, validity, lifetime, and qualification.
Each form produces a view (a pointer or reference), never a new value:

```zax
otherPointer := myPointer unsafe cast OtherType *     // pointer → pointer
otherView := myValue unsafe cast OtherType &          // value place → reference view
otherView := myReference unsafe cast OtherType &      // reference → reference view
restored := myAddress unsafe cast MyType *            // pointer-representation integer → pointer (A5)
```

To get a new value, form the view and construct from it explicitly:

```zax
copy : OtherType = myValue unsafe cast OtherType &
```

**Rules to preserve:**

- There is no bare by-value `unsafe cast OtherType`. The spelling cannot say
  whether it means byte copy, construction, truncation, or lifetime creation.
  Show the explicit view-then-construct form. Show the rejected bare form only
  if promotion judges that it prevents a likely misunderstanding.
- **Vacancy is preserved.** A vacant source pointer produces a vacant
  destination pointer, not a pointer to the source type's Nothing backing
  reinterpreted as the destination type. Non-vacant addresses are reinterpreted
  unchanged. This reverses the current pointer-owner sentence saying
  `unsafe cast` "preserves a vacant source's raw representation".

  ```zax
  vacantPointer : MyType *
  otherPointer := vacantPointer unsafe cast OtherType *
  // ?otherPointer is false: still vacant
  ```

  **Cost.** Preserving vacancy may require a vacancy test on the source. The
  test disappears when the source and destination Nothing representations
  coincide. It also disappears when the compiler proves that the source can
  never be vacant. That is an optimization from private proof and does not
  change source validity. The cost section should state both cases.
- **Physical vacant bits.** A programmer who wants the exact raw representation
  converts through a pointer-representation integer. That round trip does not
  preserve vacancy (A5).
- **Qualification.** The written destination type states the complete result
  qualification. `unsafe cast` may therefore weaken `immutable`, `readonly`, or
  `final`:

  ```zax
  source : Payload immutable = makePayload()
  writableView := source unsafe cast Payload mutable writable &
  ```

  For a qualifier-only change on the same type, the teaching **recommends
  `unsafe pliable`**. It keeps the recorded qualifications and bypasses them
  locally, while `unsafe cast` manufactures a differently qualified view.
  Qualifiers keeps `unsafe pliable`; its deferral sentence ("the complete cast
  lattice and exact cast syntax remain later casting design") is replaced by a
  link here.
- Strengthening needs no cast: `view : MyType readonly & = myMutableValue` is
  ordinary. Adding `immutable` to a mutable value is **not** strengthening: it
  promises that no path will ever change the value. It requires `unsafe cast`:

  ```zax
  myMutable : MyType = makeValue()
  asReadonly : MyType readonly & = myMutable                       // ordinary
  asImmutable : MyType immutable & = myMutable                     // error: mutability cannot be promised away
  trusted := myMutable unsafe cast MyType immutable &              // programmer asserts no other mutation
  ```
- `unsafe cast` verifies nothing: compatibility, qualification, invariants,
  lifetime, bounds, and layout are all unchecked. A proved-impossible
  reinterpretation is still rejected under the general safety rule.
- **Pointer roles.** For raw, `unique`, `unique shareable`, `strong`, and `weak`
  pointers, `unsafe cast` changes **only the pointee type**. The ownership-role
  transition, the transfer stance, and every lifetime rule are exactly those of
  the ordinary conversion between those roles. `unsafe cast` does not escape
  them.

  ```zax
  shared : Truck * strong = myCar unsafe cast Truck * strong              // copy: another strong owner
  observer : Truck * weak = myCar unsafe cast Truck * weak                // ordinary strong → weak
  exclusive : Truck * unique = myCar as last unsafe cast Truck * unique   // ordinary claim rules, including root
  ```

  - Control-block roles (`strong`, `weak`, `unique shareable`) still dispose
    the real allocation that their control block records. Casting a
    `Car * strong` to `Truck * strong` does not make release destroy a `Truck`.
  - A thin `unique` may also be cast, under unsafe responsibility. Whether it
    is valid depends on the program; the `Truck` view may be legitimate, and
    the compiler cannot tell. The teaching does not yet promise which type a
    retyped thin `unique` destroys. That depends on whether the root allocation
    records destructor information (see deferrals). It does make plain that the
    programmer owns the risk.

### A5. Pointer ↔ integer conversion

**Teaching shape.**

```zax
myAddress : UPointer = myPointer as UPointer            // safe: an observation
myRestored : MyType * = myAddress unsafe cast MyType *  // unsafe: asserts the address is valid for MyType
```

**Rules to preserve:**

- `pointer as UPointer` is a protected intrinsic signature on the pointer domain. It is
  not unsafe, because observing an address cannot break memory safety. It loses
  provenance: the integer proves nothing if later converted back.
- The integer role must match the pointer's memory domain: `Near.UPointer` for a
  near pointer and `Far.UPointer` for a far pointer.
- **`IPointer` is retired**, in every memory domain. Pointer differences use
  `PointerDelta`, which is `UPointer delta type` in each domain. By the
  integer `delta` rule, that type covers the complete signed difference range.
  `UPointer` becomes a one-sided role, like `Word` or `Byte`: a signedness
  operation returns the unnamed exact intrinsic counterpart. C `intptr_t` and
  C++ ABI mapping are workaround-level interop concerns (see deferrals).
- A vacant pointer converts to its raw representation. That value is
  non-portable, and converting it back does not restore vacancy.
- Integer to pointer is a new source role for `unsafe cast`. The destination is
  still written `Type *`, so it keeps the existing pointer result shape. It needs
  no exception to the no-by-value rule.
- Integers keeps the capacity roles and replaces "actual pointer-object transfer
  remains future pointer/lifetime work" with a link to these forms. The deeper
  provenance, alignment, segment, and comparability questions remain in raw
  pointer mechanics.

**Not to teach:** `myPointer unsafe cast UPointer` (by-value), `address of`, or
`unsafe from` spellings. These were discussion candidates.

### A6. Pointer and reference formation: no change, only teaching

**Teaching shape** for the casting document's tour, linking to the owners:

```zax
value : MyValue
pointer : MyValue * = value      // take an address: implicit, no cast
inferred : * = value             // pointee type inferred
view : MyValue & = pointer.      // dereference: explicit trailing `.`
```

**Rules to preserve:**

- Nothing instances remains correct and unchanged. Unproved `pointer.` is
  allowed; proved vacancy is an error; runtime vacancy reaches the pointee
  type's Nothing instance under its policy.
- Writes to compiler-provided Nothing backing are already covered: a proved
  write is an error, and an unproved write has no per-store check. The future
  narrow boundary is already live as the provisional `possible-nothing-write`
  and `possible-nothing-read-trap` entries in Nothing instances, safety, and the
  raw analysis-control registry. `031` adds nothing there.
- **No hidden dereference.** Initializing a reference from a pointer without the
  trailing `.` does not dereference. Binding a reference to a pointer means a
  reference to the pointer place, as `pointerView : MyValue * readonly & =
  owner.pointer` in lifetimes shows. Show it only if promotion judges the
  confusion likely.

**Maintainer principle, reaffirmed, not new design.** Proof portability follows
the selected-contract model in safety and analysis. A proof the contract
requires succeeds, so the form needs no marker. A proved-false case is an
error, and any compiler may diagnose more as analysis improves. Otherwise a
narrow `unsafe` marker is required. Compiler innovation proceeds through
explicitly selected extension contracts and later contract versions. No panic
fallback is added.

### A7. Interior ownership: `inner`

This replaces the current `anchored` pointer role and `anchored by` form.

**Teaching shape.** Sometimes code needs an owning pointer to a member of a
managed allocation, and that pointer must keep the whole allocation alive. It
must also be usable anywhere an ordinary `strong` pointer is accepted:

```zax
Engine :: type {
  rpm : Integer
}

Car :: type {
  engine : Engine
}

inspectEngine final : ()(engine : Engine * strong) = {
  use(engine.rpm)
}

car : Car * strong = makeCar()

engine : Engine * strong = inner car.engine   // shares car's control block
inspectEngine(engine)                         // an ordinary strong pointer; no special flavor
```

`engine` points at `car.engine` and participates in the same control block and
strong count as `car`. When the last owner of either kind releases, the whole
`Car` allocation is disposed.

**Special path operation.** `inner` looks like a pre-unary phrase, but its
operand is a **path**, not a value. Zax has a few such path operations, all
special cases: `inner`, the outer-cast family, and `offset of`. Programmers
cannot write operators that take paths, and mixfix does not accept paths. The
teaching says this directly so readers do not generalize from it.

Mechanically, `inner` stays an ordinary phrase word. The language holds a
protected intrinsic signature (A8) for `inner` applied to a direct member path
whose root is a pointer. When source has that shape, the protected intrinsic signature
claims it and the operand is treated as a path. Otherwise ordinary phrase lookup applies, so a
programmer type may still declare its own `inner`. The word is not a hard
keyword.

**Rules to preserve (agent-defined at maintainer request; review during
teaching):**

- **Root.** The first segment is a pointer expression. The protected intrinsic signature
  claims every pointer root, including unsupported ones, which it then rejects.
  Changing a root from `strong` to raw therefore produces an error. It does not
  silently select a programmer's `inner` on the member type.
- **Path.** One or more direct resident stored-member segments. The path may not
  cross a pointer or reference dereference, an optional payload, a variant
  alternative, a dynamic array element, an unmanaged overlay, or a separately
  allocated member. Those places can disappear or relocate while the allocation
  lives.

  ```zax
  wheel : Wheel * strong = inner car.axle.wheel      // direct chain: valid
  driver : Driver * strong = inner car.driver        // error if `driver` is a pointer member: path crosses a pointer
  ```

  Rejecting pointer members is what keeps the refactor safe. If `car.engine`
  later becomes an `Engine * strong` member, `inner car.engine` becomes an error
  rather than quietly copying the member pointer.
- **Result role follows the root.**

  | Root | Result |
  | --- | --- |
  | `strong` (and `strong atomic`) | Same role; increments the shared strong count |
  | `weak` (and `weak atomic`) | Same role; no ownership acquired |
  | `unique`, `unique shareable` | Error: would create a second owner of a uniquely owned allocation |
  | raw | Error: no control block to share |

- An interior pointer may itself be a root: `inner engine.crankshaft` shares
  the same control block.
- **Plain member access never manufactures ownership.**
  `engine : Engine * strong = car.engine` is an error: a place is not an owner.
- **Representation.** `strong`, `weak`, and `unique shareable` pointers are fat:
  they record the target and the control block separately. That was always
  expected for managed shared roles, so the uniform type costs nothing extra.
  `unique` stays thin and fast, which is why an interior target can never
  become `unique`. The claim below explains the consequence.

**Claiming `unique` from a shared or shareable pointer.**

```zax
if car is allocation root {
  exclusive : Car * unique = car as last
  // can still fail on the strong count or weak observers, not on root-ness
}
```

- The current claim conditions (one strong owner, no weak observers) gain an
  explicit **allocation-root** condition. The control block knows the root.
- A compiler-proved interior target makes the claim an error. For example:
  `engine as last` into `Engine * unique` right after `inner`.
- At runtime, a non-root claim fails like the existing failures: the
  destination is a vacant `unique`. Under `as last`, the source is left in its
  terminal, destruction-only state. This is why the query matters.
- `is allocation root` is a protected post-unary query in the `is` family. Its
  answer depends only on **which place the pointer currently targets**. It is
  never a race, unlike the momentary `strong count probe`, so it is not a
  `probe`. A pointer that is repointed, or a new pointer produced by
  `outer cast` (below), may target the root and then answers `true`.
- A `unique shareable` destination may hold an interior target, because it
  retains the block. Shedding `unique shareable` to `unique` requires the
  allocation root and fails the same way otherwise.
- Thread handoff: the pointers rule that a `strong` copy crosses threads only
  "through unique ownership" must say `unique shareable` for interior targets,
  or `atomic` roles.

**Returning to the container: role-preserving `outer cast`.**

`inner` goes from a container to a member while keeping ownership. The existing
`outer cast` family goes back. When the operand is a managed pointer, the result
has the **same role** and shares the same control block:

```zax
car : Car * strong = makeCar()
engine : Engine * strong = inner car.engine

sameCar : Car * strong = engine outer cast Car.engine   // same control block
// sameCar is allocation root: true
```

- No new word is needed. The three outer-cast forms keep their meaning:
  - plain `outer cast` needs the contract-required proof that the pointer came
    from exactly that member path;
  - `unsafe outer cast` asserts that origin;
  - `tracked outer cast` returns a checked result.
- It still crosses one immediate boundary at a time. Going out one step from
  `inner car.axle.wheel` produces an interior `Axle * strong`, not the root.
- `weak` operands produce `weak` results. Raw operands keep today's outer-cast
  behavior.
- A false `unsafe outer cast` assertion cannot corrupt ownership accounting.
  The result shares the operand's real control block; only its target place
  would be wrong.
- **Control-block check (aligned extension).** A managed operand's control
  block knows the allocation root and its type. When the outward step reaches
  the root, the checked form compares the target with the root plus the member
  offset. It needs no `outer tracked` member type. A mismatch produces a vacant
  pointer, matching the other ownership-claim failures, rather than an optional
  wrapper. Where the outward step does not reach the root, the ordinary
  `outer tracked` requirement still applies.

  ```zax
  // Illustrative: checked form over a managed operand whose outward step reaches the root.
  maybeCar : Car * strong = engine tracked outer cast Car.engine
  // vacant if `engine` did not come from a Car root's `engine` member
  ```

**One rule for `tracked outer cast` (aligned).**

- `tracked` means **checked at runtime**. The check uses either the member
  type's `outer tracked` capability or, for a managed pointer whose outward
  step reaches its allocation root, that pointer's control block.
- **The failure shape follows the operand**:
  - a reference operand produces an optional reference, because references
    cannot be vacant;
  - a pointer operand produces a vacant pointer of the same role, as failed
    claims, weak acquisition, and `@!` do. This includes raw pointers, which
    composition does not currently specify.
- `intent<redundant-outer-tracking>` is unchanged.

Composition teaches this as one rule with both examples side by side, so the
form does not read as irregular:

```zax
checked : Car & ? = engineReference tracked outer cast Car.engine       // reference in, optional out
checkedOwner : Car * strong = engineOwner tracked outer cast Car.engine  // pointer in, vacant on failure
```

**Erasure keeps the interior target (aligned).**

`OpaqueOwner` records the target place and target type, not only the control
block and allocation root. Recovery and `is type` compare the **target** type,
so an erased interior pointer round-trips exactly:

```zax
engine : Engine * strong = inner car.engine
opaque : OpaqueOwner strong = engine
engineAgain : Engine * strong = opaque             // succeeds: same target, same control block
carAgain : Car * strong = engineAgain outer cast Car.engine   // reach the root explicitly
```

This replaces the current rule that erasing an interior owner keeps only the
allocation root. The physical layout remains to be designed. `OpaqueOwner` may
become fatter, which is acceptable if no better solution is found (see
deferrals).

**Claiming `unique` from a recovered root.** The claim succeeds once every other
strong owner is gone. That includes interior owners such as `engine`:

```zax
reset engine                                  // release the interior owner
exclusive : Car * unique = sameCar as last    // succeeds if sameCar is now the only owner
```

If `car` still exists, it is also an owner, and the claim fails with a vacant
destination.

**Replacement intent (kept; teaching must be rebuilt).**

The existing teaching never shows replacement happening, so readers cannot tell
what the acknowledgement protects. Promotion teaches it from this example:

```zax
car : Car * strong = makeCar()                // the Car pointee place is replaceable

intent<inner-pointer-tracks-replacement>{     // illustrative category name
  engine : Engine * strong = inner car.engine
}

car. .= makeCar()      // complete replacement: the old engine ends; a new engine is built in the same place
use(engine.rpm)        // defined: reads the new engine, not the one that existed when `engine` was created
```

The concern is **not** that other holders keep seeing an old member; the old
member no longer exists. The concern is that an owning pointer silently comes to
designate a different, newly built instance.

- For comparison, a plain member reference in the same situation is a
  safe-language **error** at its next use (lifetimes: "Direct-member references
  cross a renewal boundary"). Reaching the successor through it needs a narrow
  `unsafe` permission.
- An interior owning pointer lives too long for per-use checking. Its behavior
  after replacement is therefore defined: it always reaches the completely
  established successor, never a half-built or ended instance. The programmer
  acknowledges that surprise once, when creating the pointer.
- The requirement applies only when the target or an enclosing direct place may
  be completely replaced. A final or non-replaceable container needs no
  acknowledgement.
- It is intent, not `unsafe`, because nothing is invalid.
- The category is renamed from `anchored-pointer-tracks-replacement` during the
  intent-category review. The name above is illustrative.

### A8. Terminology: protected form and protected intrinsic signature

**Aligned terms:**

- **Protected form** (new; replaces "reserved phrase form"). An exact form that
  no programmer declaration may claim for any receiver. Examples:
  - `as copy`, `as deep`, `as move`, `as last`;
  - `type of`, `size of`, `alignment of`, `offset of`;
  - `is constant`, `is final`, `is immutable`, `is readonly`;
  - the structural `as shape`, `as layout`, `as compatible ...`, and
    `as coercive ...` forms, and `anchor`;
  - `unsafe cast`;
  - `outer cast`, `tracked outer cast`, and `unsafe outer cast`.
- **Protected intrinsic signature** (existing term, defined in operators and
  terms; kept unchanged). An exact signature whose operands belong to closed
  intrinsic families. The language owns that signature; the same form stays
  extensible for programmer operand types. Examples:
  - the exact `Boolean` logical phrases;
  - integer, fixed-point, and floating-point operators and conversions;
  - `pointer as UPointer`;
  - the pointer-domain queries `liveness probe`, `strong count probe`, and
    `is allocation root`;
  - `inner` applied to a pointer-rooted member path.

**Classification test.** If a programmer type may still declare the same
spelling for itself, the operation is a protected intrinsic signature, not a
protected form. `inner` passes this test: a programmer type may declare its own
`inner`. What makes `inner` unusual is that its operand is a path rather than a
value, the special path operation taught in A7. That is a property of `inner`
itself. Neither term expands to cover it.

Structural `anchor` remains a protected form and is unaffected by retiring the
pointer-ownership `anchored` role and `anchored by`. The numeric contextual
anchor and mixfix receiver anchor are likewise untouched.

"Reserved phrase form" is replaced by "protected form". The held-for-later uses
become future-work wording: "reserved in concept" for the unnamed
type-information operation, and "reserved for future numeric work". Unrelated
uses of "reserved" stay, such as a reserved control block or reserved storage.

### A9. Pointer observation and metadata operations

These come from the legacy `basics.md` list. Their eventual owner is pointers or
raw pointer mechanics, not casting. The casting document does not teach them.

- **`strong count probe myShared`.** A momentary strong-count snapshot, paired
  with `liveness probe`. It may be stale immediately. Owner: pointers.
- **`is allocation root`.** See A7. Owner: pointers.

Like `liveness probe`, `strong count probe` and `is allocation root` are
protected intrinsic signatures on the pointer domain, not protected forms (A8).
- **`allocator of myValue`.** Returns the arena as a **reference**. It requires
  contract-defined proof that the operand is a known allocation. Otherwise use
  `unsafe allocator of`. Both forms are non-failing, so a reference fits; a
  pointer would be needed only for a checked variant. The result type is a
  placeholder, `OpaqueReferenceObserver`, recoverable with `is type`, until the
  arena interface exists. A blockless `unique` qualifies only if it retains its
  arena, which is already open in raw pointer mechanics. Owner: raw pointer
  mechanics (arena interfaces) until that interface exists.
- **`overhead as`.** Protected access to a pointer's control-block description,
  returning a pointer to a yet-undefined type. Capture as raw pointer-mechanics
  pressure.
- **`overhead size of`.** Captured as **design pressure only**. The legacy
  host/target variants link to the raw compile-time execution material. Custom
  control blocks are unsettled and considered unlikely; do not treat them as
  motivation.

### A10. Result slots are constructed with `.=` (aligned 2026-09-24)

**Why this arose.** Post-promotion review found that the promoted custom-`as`
example was invalid:

```zax
  ) readonly = {
    result.degrees = degrees * 9.0 / 5.0 + 32.0   // error: use before result construction
    return result
  }
```

An ordinary result slot begins unconstructed, and ordinary `=` never
constructs. `= :` on the result would make it valid, but the maintainer
rejected that as the fix. A result initializer is part of the **prototype**: it
obligates every caller and every other implementation of that prototype to
pre-construct the result. The need here is local to one body, so it must not
change the callee's contract. The only other valid form today is the verbose
`result.+++()`.

**Teaching shape.**

```zax
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
```

`.=` makes a place hold a newly constructed value. On a result slot that is not
yet constructed, `.=` constructs it. On a live result, `.=` replaces it as
today.

**Rules to preserve:**

- **First construction reads like a declaration initializer.** `result .= src`
  means exactly what `result : T = src` would mean:

  ```zax
  result .= value       // construct from value
  result .= [{ value }] // same meaning; a packet matters only for several arguments
  result .= [{}]        // zero-input construction; for ordinary types, same as result.+++()
  ```

  For an optional result, `result .= [{}]` constructs a present optional with a
  zero-input payload, as `present : MyValue? = [{}]` does. The type default,
  which is absent, keeps its existing spellings, such as `return (: Item?)`.
- **First construction needs no replacement permission.** It needs neither
  `varying` nor replacement authority, so a `readonly immutable final` result is
  constructed and finalized in one step. The replacement requirements (type-side
  and declaration-side `varying`, writable access, and a replacement hook or
  fallback) apply only when the result is already live. A second `.=` on a
  `final` result is therefore a "replacement not permitted" error.
- **The expression evaluates to access to the new value**, consistent with the
  other `.=` forms.
- **Static tracking only.** When the compiler cannot tell whether the result is
  constructed at a `.=`, for example in a loop or after paths that disagree,
  that is an error. Construct outside the ambiguous region. No runtime flags are
  involved.
- **Unchanged rules:**
  - ordinary `=` never constructs, so `result.degrees = x` before construction
    is still an error;
  - `result.+++(...)` stays legal;
  - `= :` stays a prototype-level opt-in;
  - a path either completes the result itself and uses bare `return` or
    fallthrough, or returns a value; returning a value into an already
    constructed result is an error, never an implicit assignment or
    replacement;
  - an `except` exit constructs only its exceptional result.
- **Out of scope for `031`**, kept as current or moved to `032`:
  - constructor-member `_.member .= ...`;
  - declaration construction;
  - `unsafe ???` places, which are type-declared unconstructed places and a
    different category;
  - replacement constructors and destructors, which are unchanged.

**Owners:** function invocation (result slots), with supporting edits in the
operator catalog's `.=` row, the `.=` requirements in construction and
destruction, and the casting example.

**Also aligned for the same promotion:**

- The casting-document opening becomes positive-first: "Zax offers several
  operations where many languages offer one cast, because speed, safety, and
  efficiency matter more here than one flexible spelling. You choose the
  operation that matches your intent, from a free reinterpretation of existing
  memory to a conversion that builds a new value."
- The pointer shedding explanation uses the maintainer's revision, which
  separates its claims and uses "a" for conceptual pointers, with "cannot".
- The `strong`-to-`weak` example needs no change: `myCar` is declared `strong`
  in the same block.

### Proposed `032` initiating concern (captured; not created)

**Concern.** Should `.=` mean construction and replacement construction
everywhere, rather than only for result slots?

**Real design pressure.** Current teaching now constructs **result slots** with
`.=` (A10). `032` must start from that fact: promoted documentation already
teaches `result .= [{ ... }]`. The motivation is real:

- ordinary `=` never constructs;
- `= :` binds the whole prototype;
- `result.+++()` is verbose.

Once results use `.=`, readers will ask why other construction sites do not.
The maintainer's consistency question is concrete:

```zax
// Illustrative only: would declarations construct with `.=`?
myType : MyType .= value
```

**What `032` must produce:**

- the design pressure above, with the `Celsius` example and the rejected `= :`
  fix;
- what the syntax would have to be in every construction scenario if `.=` meant
  construction and replacement construction, including:
  - declarations;
  - constructor members (`_.member .= [{ ... }]` as the readable form of
    `_.member.+++(...)`);
  - multiple results;
  - result mappings and routing;
  - packets;
  - optional and variant payloads;
  - allocation initializers;
  - captures;
  - any other construction site found;
- the consequences for teaching, diagnostics, and existing examples;
- a recommendation, either to adopt `.=` broadly or to abandon the expansion
  and keep `.=` local to results and replacement, for the maintainer to decide.

**Starting constraints:**

- ordinary `=` never constructs;
- first-construction `.=` reads like a declaration initializer;
- a path either completes a result or returns a value;
- `.=` replacement permissions apply only to live places.

**Reading scope** (to propose at closure): construction and destruction,
function invocation, declarations and bindings, optional values, variants,
operator catalog, and `language/casting.md` as the first taught use.

---

## Legacy dispositions

### `casting.md`

| Legacy section | Disposition |
| --- | --- |
| Opening and disposition banners | Routing only; retire with the page |
| String-conversion disposition | Already consumed by strings and string-runtime raw input |
| Pointer casting using `unsafe as`; `Unknown *` | Superseded by `unsafe cast` (A4) and the `Opaque*` family |
| Casting a by-value type into a pointer | Implicit formation is current (A6). Explicit `as MyType *`/`as *` is not built in (A2). The dangling-pointer example is lifetime teaching; before deletion, confirm that lifetimes covers returning a pointer to a local |
| Pointer dereference and reference formation | Already consumed by Nothing instances; unchanged (A6) |
| Structural shape and layout conversion | Already consumed. The casting tour summarizes it with a link (A1) |
| `as` operator overloading | Rewritten as A2 with a current example |
| By-reference custom `as` error example | Superseded: custom `as` may target pointer or reference types (A2) |
| Disable `as` operators | Retired (A2). Partial-type pressure captured below |
| Casting as `default` | Retired (A3) |

**Recommendation:** delete `casting.md` in the promotion change set, after a
live-link sweep. The dry run found live links in `index.md` and in the
Source/Provenance rows of `integers.md` and `strings-and-characters.md`.

### `basics.md` historical operator list

Remove each entry that `031` consumes, rejects, or obsoletes. Keep the rest.

| Legacy entry | Disposition |
| --- | --- |
| `outer of`, `unsafe outer of` | Rejected; `outer cast` family is current |
| `lifetime of` | Superseded by `inner` (A7) |
| `unsafe lifetime of` | Retired; unsafe interior ownership pressure stays in raw pointer mechanics (deferrals) |
| `unsafe copy as` | Retired; `unsafe cast` plus `as copy` covers data. No callable-erasure capture requested |
| `count of` (variadic) | **Kept** in `basics.md`; unrelated to casting |
| `count of` (type) | Retired; intent unrecoverable |
| `overhead count of` | Superseded by `strong count probe` (A9) |
| `overhead as` | Consumed into raw pressure (A9) |
| `overhead size of` and its host/target variants in the sizing lists | Consumed into raw pressure (A9). The sizing-list lines may remain until the host/target sizing concern is dispositioned, since they also carry `size of` variants |
| `allocator of` | Consumed (A9) |
| `Unknown`, `Void` (`basics.md` near line 449) | Retired; the `Opaque*` family replaces them. Foreign `void *` pressure goes to raw interop |

---

## Open items

**Resolved (dry-run item B4):**

- **Casting document name.** The file is `language/casting.md`, titled "Zax
  conversions and casts".
- **`cast` versus `as` teaching pattern.** It is not taught as a rule. The
  document opens with a task-first "which operation do I want?" choice.

**Remaining, not blocking:**

1. **`inner` rule review.** The A7 rules were defined by the agent at maintainer
   request. The maintainer reviews them through the promoted teaching draft.
   The role-preserving `outer cast`, its control-block check, the `tracked`
   rule, and erasure round-tripping are aligned.

---

## Captured deferrals

Each entry gives the finding, its destination, and what reactivates it.
Destinations are candidates for the dry run.

| Finding | Destination | Reactivation | Constraint on now |
| --- | --- | --- | --- |
| A `partial` route that adds `as` to a protected domain (`myInt as MyTally`) | [raw partial types](../raw/partial-types.md) | Partial-type or protected-domain work | Teach `MyTally from myInt` as the current route |
| A `partial` wanting to disable a type's declared `as` ("feels wrong") | raw partial types | Partial-type work | No disable mechanism is taught |
| Restricting which destinations one custom `as` accepts | [raw generics](../raw/type-parameters-and-generics.md), beside "Dependent value result types" | Generic constraints | Teach "one declaration, one intended destination" |
| Expressing protected numeric conversion generically (a generic integer to a generic float) | raw generics | Generic numeric work | None; conversions are already protected signatures |
| Rounded numeric conversion spelling | Already live in floating-point scalars ("exact source phrase remains operator work") | Numeric operator work | None new |
| Helper for an array view or iterator over raw memory bytes | [raw pointer mechanics](../raw/pointer-and-arena-mechanics.md), pointer provenance and conversion; the shape touches slices | Raw-memory, FFI, or serialization pressure | Raw pointers remain the byte-access vehicle |
| Pointer-to-integer provenance, alignment, segments, comparability | Already live in raw pointer mechanics | Pointer provenance work | A5 forms introduce no provenance promise |
| `allocator of` result type and arena interface | raw pointer mechanics, arena interfaces | Arena interface work | Placeholder `OpaqueReferenceObserver` result |
| `overhead as` result type; `overhead size of` | raw pointer mechanics, control blocks; host/target link to [raw compile-time execution](../raw/compile-time-execution.md) | Control-block or layout-reflection work | Design pressure only |
| Unsafe interior ownership (successor to `unsafe lifetime of`) | raw pointer mechanics: update the illustrative `unsafe anchored by` to an `inner`-shaped illustration | Unsafe ownership work | Ordinary `inner` requires a direct path |
| Deeper direct chains | Resolved by A7 if accepted; remove the deferral from raw pointer mechanics | — | — |
| Foreign `void *` | [raw interop](../raw/interop.md) | FFI work | No `Void` type in Zax |
| Possible `weak count probe` | raw pointer mechanics | Demand for weak-count observation | Only `strong count probe` is aligned |
| `OpaqueOwner` physical layout once it records the target place and target type (possibly fatter; accepted if no better solution exists) | raw pointer mechanics, beside type-erased ownership | Opaque-owner representation or interior-pointer layout work | Semantics are fixed: erasure round-trips the interior target |
| A root allocation recording basic destructor information, so that a retyped thin `unique` can still destroy the constructed type | raw pointer mechanics, beside how a blockless `unique` retains its destructor, size, and arena | Blockless-`unique` representation work | Teaching makes no promise about which type a retyped thin `unique` destroys |
| C `intptr_t` and C++ ABI mapping after `IPointer` retirement | raw interop | FFI or ABI work | Workarounds only; no Zax `IPointer` |

---

## Promotion consequences (preliminary, not the dry run)

**Superseded by the 2026-09-23 dry run below**, which corrects and completes
this list. It is retained only until the dry run's change set is aligned.

The dry run must confirm this list. It records the files that aligned findings
touch, so none is forgotten:

- **New casting document** (A1, A2, A4, A5; tour hooks for A3, A6, A7). It needs
  a teaching-first order: opening tour, custom `as`, `unsafe cast`, then
  pointer ↔ integer.
- **Structural shapes:** reduce view-shaped `unsafe cast` to its structural
  applications plus a link; update the Owns metadata.
- **Pointers and arenas:**
  - replace the anchored interior pointers section with `inner` (A7);
  - add the allocation-root claim condition, `is allocation root`, fat versus
    thin representation, and the thread-handoff wording;
  - add `strong count probe`;
  - reverse the `unsafe cast` vacancy sentence (A4);
  - remove "pointer-cast syntax" from Does Not Own.
- **Lifetimes and references:** update the anchored-pointer paragraph to `inner`.
- **Nothing instances:** no semantic change; check any `unsafe cast` vacancy
  wording.
- **Qualifiers:** replace the "Unsafe casts and new values" deferral with a
  link and the `unsafe pliable` recommendation.
- **Integers:** link pointer-representation roles to A5. Retire `IPointer`:
  - remove the `Near.IPointer`, `IPointer`, and `Far.IPointer` table entries;
  - replace the `IPointer <-> UPointer` relational-pair example;
  - state that `PointerDelta` is `UPointer delta type` in each domain.

  **Raw generics** also replaces its `IPointer <-> UPointer` example.
- **Composition:** role-preserving `outer cast` for managed operands, and the
  control-block check when the outward step reaches the root, including its
  vacant-pointer result versus the optional result of `tracked outer cast`.
- **Operator catalog:**
  - retire `as default`;
  - rename the reserved-form sections to protected forms;
  - add `inner`, `is allocation root`, `strong count probe`,
    `pointer as UPointer`, and the integer source role of `unsafe cast`;
  - drop bare `unsafe as` from the open language-defined list (it is an
    ordinary programmer phrase).
- **Operators, operator phrases, terms, transfer stances:** the A8
  terminology sweep; the terms entries for protected form, protected intrinsic
  signature, and `inner`, and remove anchored-pointer wording.
- **Intent acknowledgements:** rename the anchored-pointer replacement intent
  when the category review occurs.
- **Raw pointer mechanics, raw partial types, raw generics, raw interop:** the
  captured deferrals above.
- **Legacy:** delete `casting.md`; trim `basics.md`; sweep `index.md` links.

**Sweep hazard.** "Anchored" and "anchor" are also used for structural source
anchors, numeric contextual anchors, and mixfix receiver anchors in many
owners. Only the pointer-ownership uses change.

---

## Withdrawn or rejected

This is provenance only. Promoted owners do not teach any of it.

- The notes' proof-or-`unsafe` rule for `pointer.` dereference was withdrawn.
  Nothing instances already handles dereference and writes (A6).
- Panic-checked dereference, an optional checked dereference form, and a hidden
  no-`.` dereference: rejected.
- `outer as` / `unsafe outer as` (the notes' wording) and legacy `outer of`:
  rejected in favor of the `outer cast` family.
- `as default`: retired.
- `anchored` pointer role and `anchored by`; `inner from` (collides with the
  `from` admission and `each from` families); `inner cast` (implies the member
  is the pointer); plain `container.contained` as ownership; fat `unique`
  pointers to permit interior uniques: rejected in favor of A7.
- Bare by-value `myPointer unsafe cast UPointer`, `address of`, and
  `MyType * unsafe from address` spellings: superseded by A5.
- Viewing the pointer object through `unsafe cast UPointer &` as byte access:
  rejected. It yields an integer view, not bytes; see the raw byte-view
  deferral.
- A lint or analysis category for programmer phrases containing `unsafe`:
  rejected (A2).
- `Void` / `Unknown` types: rejected.
- `IPointer` (all domains): retired. `PointerDelta` covers differences.
- Forbidding `unsafe cast` from retyping a thin `unique`: rejected. It is
  allowed under unsafe responsibility (A4).
- The misreading of the replacement intent as "old holders keep seeing the old
  member": corrected (A7). The intent is kept.

## Dispositions and promotion dry run

### Addendum dry run for A10, 2026-09-24: **PASS**

This dry run covers only the post-promotion revision: result-slot `.=`
construction, the casting opening, and the pointer shedding phrasing. The
original promotion below is already applied.

- **Ownership.** Function invocation owns result-slot construction. A10 is
  taught there, immediately after "An initially unconstructed result may
  instead be constructed later", where readers already meet `result.+++(source)`.
  The other owners only acknowledge the rule and link to it.
- **Change set:**
  - `language/function-invocation.md`: add `.=` first construction of a result
    slot, with its declaration-initializer reading, the replacement boundary,
    and the static-tracking error; update the tracked-construction rejection
    list so that a second `.=` is replacement, not double construction;
  - `language/construction-and-destruction.md`: in "Why replacement exists",
    note that `.=` on a result slot not yet constructed performs first
    construction and needs no replacement permission, with a link to function
    invocation;
  - `language/operator-catalog.md`: the `.=` row mentions first construction of
    an unconstructed result slot;
  - `language/terms.md`: "Reconstructive replacement" notes the result-slot
    first-construction case;
  - `language/casting.md`: the positive-first opening, and the `Celsius`
    example using `result .= [{ ... }]` with one sentence explaining it and
    linking to function invocation;
  - `language/pointers-and-arenas.md`: the maintainer's shedding phrasing.
- **Teaching checks.**
  - The casting example is now valid and needs no new concept beyond one
    linked sentence.
  - The function-invocation addition leads with source.
  - No retired form is shown.
  - The one error shown, `result.degrees = x` before construction, is a
    mistake a programmer would plausibly make.
- **Deferred.** The broader `.=` question is the proposed `032` concern
  recorded above. No current owner cites it.

### Current result: **PASS** (re-run 2026-09-23)

The first run failed on B1–B4 below. The maintainer aligned all four, and the
resolutions are folded into the aligned findings:

- **B1.** Erasure keeps the interior target, as option (b). This is recorded in
  A7, with the layout captured as design pressure in the deferrals.
- **B2.** `inner` is a protected intrinsic signature, not a protected form.
  The existing term is kept, and "protected form" does not expand. This is
  recorded in A8, with the pointer-domain queries reclassified the same way.
- **B3.** `tracked` means checked at runtime, and the failure shape follows the
  operand: a reference gives an optional, and a pointer, including a raw
  pointer, gives vacancy. This is recorded in A7.
- **B4.** The file is `language/casting.md`, titled "Zax conversions and casts",
  with a task-first opening. The `cast`/`as` pattern is not taught as a rule.

On re-run, every check below passes:

- each finding has one owner;
- the change set below is exact;
- deferrals have live destinations;
- the teaching plan satisfies the cold-reader, vocabulary, concrete-consequence,
  teaching-before-reference, and anti-scenario checks.

The one remaining open item, maintainer review of the `inner` rules, is
satisfied through the promotion draft and does not block.

PASS does not authorize promotion.

### First run, 2026-09-23: FAIL

Recorded for provenance. The recommendations in each item below were the ones
aligned.

### Blocking items (all resolved)

#### B1. Erasing an interior owner into `OpaqueOwner`

Current pointer text: "Erasing an anchored interior owner retains the enclosing
allocation root, not the interior target. Recovery therefore returns only a
compatible allocation-root pointer." Now that an interior pointer is an ordinary
`strong`, that rule makes erasure silently change what the pointer designates:

```zax
car : Car * strong = makeCar()
engine : Engine * strong = inner car.engine

opaque : OpaqueOwner strong = engine
engineAgain : Engine * strong = opaque   // current rule: vacant, because opaque remembers only the Car root
carAgain : Car * strong = opaque         // current rule: succeeds, which widens the pointer to the whole Car
```

This reintroduces the pointer-flavor problem that A7 was designed to remove:
code must know whether an erased `strong` was interior.

- **Option (a).** Keep root-only erasure and teach that erasure widens an
  interior owner to its root.
- **Option (b), recommended.** `OpaqueOwner` keeps the target place and target
  type as well as the control block. It is already fat. Recovery and `is type`
  compare the **target** type, so `engineAgain` succeeds. To reach the root,
  recover the `Engine * strong` and use the role-preserving `outer cast` (A7).
  Erasure then round-trips exactly, like every other pointer.

Either choice changes the pointers section on `OpaqueOwner` and its `is type`
wording.

#### B2. "Protected signature" collides with an existing term

A8 introduced **protected signature**. Operators and terms already define
**protected intrinsic signature**: "an exact operator signature whose every
operand belongs to a closed intrinsic family." That term covers the exact
`Boolean` phrases and the scalar surfaces. Two terms for nearly one idea would
make readers look for a difference that does not exist.

**Recommendation:**

- Keep the existing term **protected intrinsic signature**. `myPointer as
  UPointer` fits it, since pointer and pointer-representation operands are
  intrinsic.
- **Protected form** replaces "reserved phrase form", as aligned.
- ~~Classify `inner` as a protected form recognized only on a pointer-rooted
  member path.~~ **Corrected during alignment:** `inner` is a **protected
  intrinsic signature**. A programmer type may still declare its own `inner`,
  so it is not a protected form. Its path operand is a property of the
  operation, not of either term (A8).

#### B3. `tracked outer cast` would mean two different things

Composition today:

```zax
Engine :: type outer tracked {
  rpm : Integer
}

checked : Car & ? = engineReference tracked outer cast Car.engine
// requires `outer tracked`; failure is an absent optional
```

A7's aligned control-block extension adds:

```zax
checkedOwner : Car * strong = engineOwner tracked outer cast Car.engine
// no `outer tracked` needed when the step reaches the root; failure is a vacant pointer
```

The same spelling now differs in two respects: which capability performs the
check, and how failure appears. Taught separately, readers would conclude the
form is irregular.

**Recommendation.** Give `tracked` one meaning and derive both differences from
the operand, so that promotion teaches it as one rule:

- **`tracked` means "checked at runtime".** The check uses the member type's
  `outer tracked` capability or, for a managed pointer whose outward step
  reaches its allocation root, that pointer's control block. The member type
  pays nothing in the second case.
- **Failure shape follows the operand, as elsewhere in Zax.** A reference
  operand produces an optional reference, because references cannot be vacant.
  A pointer operand produces a vacant pointer of the same role, matching failed
  claims, weak acquisition, and `@!`.
- **Consequence to confirm.** A raw-pointer operand of `tracked outer cast`
  would also report failure by vacancy. Composition does not currently state a
  pointer-operand result, so this is a clarification, not a reversal.
- `intent<redundant-outer-tracking>` is unchanged.

#### B4. Casting document name and opening pattern

- **Filename.** The recommendation is `language/casting.md` with the title
  "Zax conversions and casts". The filename matches the term readers search
  for and the notes' vocabulary; the title states the scope honestly.
- **"`cast` reinterprets, `as` converts".** The recommendation is **not** to
  teach this as a rule. Coercive `as` also yields views, and a rule with a
  visible exception teaches poorly. Open the document with a task-oriented
  choice instead (see the teaching plan).

### Passing checks

**Ownership map.** Each aligned finding has one lasting owner:

| Finding | Lasting owner | Other owners |
| --- | --- | --- |
| A1 tour, A2 custom `as`, A4 `unsafe cast`, A5 pointer ↔ integer | new `language/casting.md` | Catalog lists exact forms |
| A3 stance forms | transfer stances (unchanged) | casting tour example |
| A6 formation and dereference | declarations, Nothing instances (unchanged semantics) | casting tour example |
| A7 `inner`, `is allocation root`, fat versus thin, claims, replacement intent | pointers and arenas | composition owns managed `outer cast`; lifetimes links |
| A7 role-preserving `outer cast` and control-block check | composition | pointers shows the round trip locally |
| A8 terminology | terms, operators, operator phrases, catalog | sweep sites below |
| A9 `strong count probe` | pointers | catalog |
| A9 `allocator of`, `overhead as`, `overhead size of` | raw pointer mechanics | — |
| `IPointer` retirement | integers | raw generics example |

**Cohesive teaching.** A single casting owner is justified: without it, readers
reconstruct "how do I convert or reinterpret?" from about ten documents. It
teaches and routes rather than redefining. Interior ownership stays cohesive
in pointers: one section carries the complete round trip, and composition keeps
only the outer-cast mechanics.

**Deferred material.** Every captured deferral has a live destination: raw
pointer mechanics, raw partial types, raw generics, raw interop, raw
compile-time execution, and the floating-point owner. None is routed from a
public owner into `project/raw/`. Public owners name future work by subject.

**Teaching checks:**

- **Cold reader.** The casting opening answers "which operation do I want?"
  before any mechanism.
- **Vocabulary before use.** "Protected form" and "interior pointer" are
  introduced after their first example.
- **Concrete consequence.** Every rule in A4 and A7 has a source line showing
  its effect.
- **Teaching before reference.** Form tables stay in the catalog.
- **No transcription.** Promotion writes from the teaching shapes, not the
  rule lists.
- **Anti-scenarios.** Only these invalid forms are shown:
  - bare by-value `unsafe cast`;
  - plain `car.engine` used as ownership;
  - an `inner` path that crosses a pointer member;
  - `immutable` from a mutable source.

### Teaching plan

**`language/casting.md`, "Zax conversions and casts".**

1. **Opening model.** Zax has no universal cast. A short source block shows
   three different operations side by side: a custom `as`, a numeric
   `narrowing as`, and an `unsafe cast`. Plain language explains that each asks
   a different question.
2. **"Which operation do I want?"** A task-first table, with one example and one
   owner link per row:
   - convert a number;
   - convert my type to another type;
   - admit a value into an identity or enum;
   - hand a value on with a stance;
   - view compatible storage;
   - get the container from a member;
   - own a member of a shared allocation;
   - take an address or follow a pointer;
   - store an address as an integer;
   - reinterpret memory without checks.
3. **Writing your own `as`.** The `Celsius` example, then:
   - the type-slot explanation;
   - one intended destination per declaration, with generics named as future
     work;
   - pointer or reference destinations are allowed and follow the
     returned-origin rules;
   - phrases beginning with `as` are discouraged by lint;
   - `unsafe as` is just a name and grants nothing;
   - scalar interaction: `MyTally from myInt`, or the type's own `as I32`.
4. **Unchecked reinterpretation with `unsafe cast`.** It is complete here:
   - the three view forms plus the integer source role;
   - view then construct for a new value, with the bare by-value form as the
     one anti-scenario;
   - vacancy preserved, with a source example;
   - qualification: the destination states it, `unsafe pliable` is recommended
     for qualifier-only changes, and `immutable` from a mutable source is the
     example;
   - pointer roles follow ordinary transitions, control-block roles dispose the
     real allocation, and a thin `unique` is programmer responsibility;
   - what remains rejected even under `unsafe`.
5. **Pointers and integers.** `as UPointer`, `unsafe cast` back, domain
   matching, vacant raw bits, and `PointerDelta` for differences.
6. **Costs.** The vacancy test and when it disappears; audit cost of unsafe
   forms.
7. **Diagnostics.**
8. **Boundaries.** Future work named by subject: generic conversion
   constraints, raw-memory views, and pointer provenance.

**Pointers and arenas, "Interior pointers"**, replacing "Anchored interior
pointers":

1. The problem: an owner of a member that must be accepted as an ordinary
   `strong`.
2. `inner car.engine` passed to a function taking `Engine * strong`.
3. The special path operation and path rules, with the crossed-pointer error.
4. The result-role table.
5. Back out with `outer cast`, linking to composition.
6. Root-ness and claiming `unique`: `is allocation root`, failure by vacancy,
   and the `as last` terminal-source consequence.
7. The replacement-intent example (car replaced, `engine` reads the new
   engine), explained once.
8. Cost: fat managed pointers versus thin `unique`.

### Structure proposal

- **No directory changes.** Add one current owner, `language/casting.md`.
- **Routers:**
  - `index.md`: add the new owner to both the guided list, near structural
    shapes, and the plain list;
  - remove the legacy `casting.md` entry;
  - `README.md` needs no change.
- **Retire:**
  - legacy `casting.md`;
  - the `basics.md` historical entries consumed by `031`;
  - `project/raw/casting-maintainer-notes.md` and its raw-index row, at
    closure. Provenance survives in the archived `031` record and Git history.
    Retiring the notes changes a path you have staged, so it happens only with
    explicit instruction about that boundary.
- **Remain:** all other raw files, updated as listed; the `count of` variadic
  legacy entry; the host/target sizing lists in `basics.md`.
- **Focused reading.** Readers reach the casting owner from `index.md` and from
  scalar, structural, qualifier, and pointer links that currently point at
  structural shapes for `unsafe cast`.

### Promotion change set (exact)

**New:** `language/casting.md`, as planned above.

**Current owners:**

- **Pointers and arenas:**
  - replace the anchored section with "Interior pointers";
  - update the anchored-pointer paragraphs on reset (near line 854), vacate
    (near line 921), the `OpaqueOwner` erasure paragraph (near line 1147;
    erasure keeps the interior target, and recovery and `is type` compare the
    target type), "what an
    anchored pointer keeps alive" (near line 635), diagnostics (near lines
    1550–1552), and source stability (near line 1609);
  - add `strong count probe` beside `liveness probe`;
  - add the allocation-root claim condition;
  - revise the raw-casting paragraph to link to casting and preserve vacancy;
  - revise the thread-handoff sentence;
  - in metadata, add `inner` and remove "pointer-cast syntax" from Does Not Own.
- **Nothing instances.** **This is a semantic correction, not wording only.**
  The paragraph near lines 630–645 ("`unsafe cast` is lower level: it preserves
  the raw bits") is reversed to vacancy preservation, with its example
  rewritten. Also replace "anchored" in the managed-roles vacate sentence (near
  line 701).
- **Composition:** role-preserving outer cast for managed operands, and the
  one-rule `tracked` teaching with the reference and pointer examples side by
  side (A7).
- **Structural shapes:** reduce view-shaped `unsafe cast` to its structural
  applications plus a link to casting; update Owns.
- **Qualifiers:**
  - replace the "Unsafe casts and new values" deferral with a link and the
    `unsafe pliable` recommendation;
  - update the pointer-role example `Member * strong anchored` and its list
    (near lines 1194–1197).
- **Integers:** retire `IPointer`, add the `PointerDelta` identity, and link to
  casting; replace the Source/Provenance link to deleted `casting.md` with plain
  wording.
- **Strings and characters:** the same Source/Provenance link repair.
- **Fixed-point** (near line 524), **floating-point** (near line 637),
  **endianness** (near line 120), and **arrays and slices** (near line 1014):
  retarget "`unsafe cast` behavior" links to casting.
- **Operator catalog:**
  - retire `as default`;
  - rename "Reserved phrase forms" and the stance-form wording to protected
    forms;
  - remove bare `unsafe as` from the open-forms sentence;
  - add the integer source role of `unsafe cast`;
  - update the outer-cast entry with the `tracked` rule (reference in, optional
    out; pointer in, vacant on failure);
  - list `inner`, `is allocation root`, `strong count probe`, and
    `pointer as UPointer` among the pointer-domain protected intrinsic
    signatures beside `liveness probe`, not among protected forms.
- **Operators:** the protected-form wording (near lines 330, 877, 902) and
  structural forms (near line 923) link `unsafe cast` to casting.
- **Operator phrases:** "Reserved transfer-stance phrases" and related wording
  (near lines 253–279, 656, 771) become protected forms.
- **Transfer stances:** protected-form wording where the stance forms are
  classified.
- **Terms:**
  - replace "Reserved phrase form" with "Protected form";
  - replace "Anchored pointer" and "Ownership anchor" with "Interior pointer";
  - update "Outer cast" for managed operands;
  - keep "Protected intrinsic signature", adding the classification test
    (a programmer type may still declare the same spelling) and `inner` as a
    path-operand example;
  - link "Coercive view" to casting for `unsafe cast`.
- **Safety and analysis:** the anchored-pointer intent sentence (near line 211).
- **Construction and destruction:** "direct-member anchored pointers" (near line
  1275).
- **Unions:** the managed-role list (near line 109).
- **Lifetimes and references:** the anchored owning-pointer paragraph (near
  line 518).
- **Intent acknowledgements:** the category rename, if the intent-category
  review is folded in. Otherwise the pointer owner keeps the illustrative name.

**Raw:**

- **Pointer and arena mechanics:**
  - replace the anchored material with interior-pointer framing, and drop the
    deeper-chain deferral;
  - recast the unsafe-anchoring illustration;
  - add the byte-view helper, `allocator of`, `overhead as` and `overhead size
    of`, the thin-`unique` destructor information, the `OpaqueOwner` layout
    pressure, and possible `weak count probe`;
  - update the index row wording.
- **Analysis-control registry:** the anchored intent entry (near line 218).
- **Partial types:** `partial` adding `as` to protected domains; disabling
  declared `as`.
- **Type parameters and generics:** destination restriction; generic protected
  numeric conversion; replace the `IPointer <-> UPointer` example.
- **Interop:** foreign `void *`; `intptr_t` and C++ ABI after `IPointer`.
- **Feature catalog:** route "Safe conversion using `as`" and "inner and outer
  casting" to the new owners.

**Legacy:**

- delete `casting.md`;
- `basics.md`: remove the consumed historical entries, the `Unknown`/`Void`
  lines (near line 449), and the anchored mentions (near lines 40 and 115);
- `index.md`: legacy entry removal plus the new routes.

**Validation after promotion:**

- live-link and anchor sweep, including `#anchored-interior-pointers`,
  `#view-shaped-unsafe-cast`, and `#reserved-phrase-forms`;
- the "anchor" sweep hazard;
- no current owner cites `031` or `project/raw/`;
- staged and unstaged boundaries reported.

## Promotion and closure record

- **2026-09-23, promotion applied.** The exact change set above, including the
  new `language/casting.md`. Validation found one stale anchor in intent
  acknowledgements, which was fixed, plus stale anchored-pointer and
  reserved-form residue, which was swept. Two adjustments were reported to the
  maintainer:
  - scalar-to-ordinary-type conversion is taught as construction, because
    `from` admission belongs to identity types;
  - the promoted `Celsius` example was later found invalid.
- **2026-09-24, notes retired.** The maintainer notes were entirely consumed
  and deleted, and removed from the raw index.
- **2026-09-24, A10 promoted.** Result-slot `.=` construction, the
  positive-first casting opening, and the pointer shedding phrasing, per the
  addendum dry run.
- **2026-09-24, accepted.** The maintainer accepted all changes and authorized
  commit, retirement of `031`, and push.
- **Teaching-debt observations:** none recorded.
- **Remaining open item.** Review of the agent-defined `inner` rules happened
  through the promoted text and was accepted with the changes.

### Initiating input for `032` (aligned 2026-09-24)

Created as `project/work/032-dot-equals-construction.md` from the captured
concern above, together with the maintainer's explicit clarification. `032`
must not deliver only its own judgment. It must show the implications of moving
construction from `=` to `.=`, with a representative example for each class of
scenario that changes, so that the maintainer can judge from both the agent's
opinion and the examples.
