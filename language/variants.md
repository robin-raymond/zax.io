# Zax variants

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers representing one named managed alternative, or absence, in one value |
| Applies To | Programmer-visible variant declaration, construction, selection, access, transfer, qualification, and lifetime behavior; not a formal grammar or representation specification |
| Implementation State | Not established by this repository |
| Owns | Variant wrappers and named alternatives; absent/present state; direct packets; protected contained reconstruction; reset; checked named access; wrapper and payload lifecycle; whole-wrapper transfer; body organization; variant costs, diagnostics, and source stability |
| Does Not Own | Exact switch clause mechanics ([switch, case, and default](switch.md#variant-alternative-selection)); general construction machinery ([construction, replacement, and destruction](construction-and-destruction.md)); general transfer meaning ([transfer stances](transfer-stances.md)); general qualifiers ([qualifiers](qualifiers.md)); generalized pattern matching; or ABI/layout contracts |
| Source / Provenance | Maintainer-reviewed managed-alternative design reconciled with optional, lifetime, construction, transfer, qualifier, composition, switch, source, and safety owners |

## Start with absence, then select one named payload

```zax
MyMessage :: variant {
  number : U32
  alternateNumber : U32
  text : String
  alternateText : String
  owner : MyPayload * unique
}

message : MyMessage

if !message
  print("no active alternative")

message.text .= "ready"
print(message.text)

incoming := receiveMyMessage()

switch incoming {
  case text {
    // Variant routing binds the active payload as local `text` access.
    print(text)
  }

  case ? bind other
    handleOtherPresent(other)

  case ! {
    print("absent")
  }
}
```

A variant has one live wrapper and **zero or one** active named payload:

- default construction creates absence;
- `.=` can select and construct one alternative;
- exactly one payload life path exists while present;
- changing selection ends the old payload and begins another; and
- wrapper destruction destroys the active payload when present.

Alternative names are part of the state. `text` and `alternateText` remain
different alternatives even though both store `String`.

> A variant is a managed wrapper around absence or one named payload. It is not
> an untagged view of overlapping bits.

Use an unmanaged [union](unions.md) when several typed lenses should interpret
the same passive representation without selection or payload lifecycle.

## Declaring alternatives and behavior

A variant body begins with its alternative prologue:

In variant switch mode, one alternative name both routes selection and
introduces clause-local reference-shaped access under that name. The complete
binding and coverage model is taught later under
[variant `switch`](#select-alternatives-with-variant-switch).

```zax
MyResult :: variant {
  value : MyValue
  error : MyError

  describe final : ()() readonly = {
    switch _ {
      case value
        print(value)
      case error
        print(error)
      case !
        print("absent")
    }
  }
}
```

Every per-instance stored declaration is an alternative. The first declaration
that adds no per-instance storage begins ordinary definition mode; alternatives
cannot resume afterward.

Allowed later declarations include:

- `final` functions and operators;
- `once` declarations whose state belongs to the complete type rather than
  every variant instance;
- aliases and nested types; and
- other genuinely no-instance-storage definitions.

A `final` value is still storage and therefore still an alternative. A direct
varying callable declaration would create hidden per-instance storage outside
selection and is rejected:

```zax
MyInvalidVariant :: variant {
  value : MyValue

  replaceable : ()() bound = { // error: hidden varying callable slot
  }
}
```

Store the callable explicitly as an alternative when needed:

```zax
MyCallbackChoice :: variant {
  callback : ()() bound strong
  disabled : MyDisabledMarker
}
```

### Named, forwarded, and anonymous variants

A named variant exposes its incomplete self-name while its body resolves.
Direct self payload storage is infinitely recursive; finite indirection is
valid.

Source-order or mutual relationships use:

```zax
MyChoice :: forward variant
```

The anchor must complete through a direct variant declaration or an exact alias
to an existing variant:

```zax
MyChoice :: alias variant ExistingVariant
```

An anonymous variant creates one stable compile-time identity:

```zax
choice : :: variant {
  text : String
  number : U32
}

MyChoiceType :: alias variant type of choice
```

Equal anonymous source elsewhere does not imply the same identity. Anonymous
recursive self-syntax is unavailable.

An exact alias preserves the specialized category:

```zax
MyChoiceAlias :: alias variant MyChoice
```

## Default and direct construction

Default construction is absent:

```zax
first : MyMessage
second : MyMessage = [{}]
```

Both wrappers are live and absent. The explicit zero-entry packet invokes the
same zero-input/default construction.

A variant construction packet names at most one alternative:

```zax
present : MyMessage = [{
  .text = "ready"
}]
```

The packet directly constructs `text`. It does not first construct absence and
assign over it.

Several alternative entries are invalid:

```zax
invalid : MyMessage = [{
  .number = 10,
  .text = "ten" // error: one variant cannot construct two alternatives
}]
```

Nested construction remains explicit:

```zax
MyConnectionChoice :: variant {
  connection : MyConnection
  unavailable : MyUnavailable
}

choice : MyConnectionChoice = [{
  .connection = (: MyConnection = [{
    endpoint,
    mode: selectedMode
  }])
}]
```

The inner packet constructs `MyConnection`; the outer packet selects the
`connection` alternative.

Direct construction from another equal variant type preserves its absent or
named-present state under the selected transfer stance.

## `.=` reconstructs contained state

`.=` is the protected, non-overloadable contained-reconstruction operator
shared with [optional values](optional-values.md#contained-reconstruction-on-an-existing-wrapper).

### Named-alternative target

```zax
message.text .= "first"
message.text .= [{ "second" }]
```

The left side is a destination designator, not a read of the old payload.
Either form:

1. evaluates and binds its source inputs;
2. destroys the old active payload when present;
3. constructs the named payload;
4. publishes that active name only after construction completes; and
5. returns access to the newly constructed payload.

Selecting the already active name still renews its payload lifetime.

### Wrapper-packet target

An existing wrapper can receive a complete variant packet:

```zax
message .= [{}] // reconstruct absent state

message .= [{
  .number = 42
}]
```

The packet contains zero alternative entries for absence or exactly one for
presence. This form returns access to the wrapper because its zero-entry case
has no payload.

A bare value cannot choose an alternative:

```zax
message .= 42 // error: name the alternative
message.number .= 42
```

Payload type alone is insufficient because several alternatives may have the
same type.

### Wrapper, contained reconstruction, and ordinary assignment differ

```zax
message = otherMessage    // assign state within this wrapper lifetime
message .= otherMessage   // reconstruct the complete wrapper lifetime
message.text .= "new"     // end old payload and construct text
message.text = "updated"  // checked access, then ordinary String assignment
```

The punctuation makes the lifetime choice visible:

- same-type wrapper `=` assigns state within a mutable wrapper lifetime;
- same-type wrapper `.=` reconstructs the complete wrapper lifetime;
- named-alternative `.=` reconstructs contained state;
- named-payload `=` invokes ordinary in-lifetime assignment through existing
  checked access;
  and
- postfix or named access reaches an existing payload.

`.=` has assignment precedence and right association. Named-alternative
reconstruction requires a mutable wrapper and writable path. A final or varying
wrapper place may use that contained operation because the wrapper lifetime
continues. Same-type wrapper `=` has the same mutable/writable requirement.
Same-type complete-wrapper `.=` instead requires a varying wrapper place,
declaration-side replacement permission, and writable access.

Every source input is secured before the old payload ends. An unresolved
reference into the old payload is an alias error or uses the applicable
`replacement-alias` unsafe responsibility. A panic leaves the same operation
blocked and exposes neither a partial payload nor rollback.

Complete shared ordering and alias behavior belongs to
[construction, replacement, and destruction](construction-and-destruction.md#wrapper-owned-contained-reconstruction-with-dot-equals).

## Reset returns the same wrapper

```zax
reset message
```

Ordinary reset is complete at that statement. Its returned access may be used
when another expression needs the now-absent wrapper:

```zax
cleared : MyMessage & = reset message
```

`reset`:

- destroys the active payload once when present;
- is a no-op when already absent;
- leaves the same wrapper lifetime absent; and
- returns access to that wrapper without increasing authority.

This permits explicit reconstruction:

```zax
(reset message).text .= "replacement"
```

Direct payload destruction is unavailable because it would leave the wrapper's
selection state incorrect:

```zax
message.text.---() // error
```

## Replace an already active payload value

Named-alternative `.=` is wrapper-owned selection and performs fresh ordinary
payload construction:

```zax
choice.replaceable .= [{ newValue }]
```

It does not select the old payload type's `+++ replacement`. To reconstruct the
active payload value itself, first establish a case binding or explicit
reference:

```zax
switch choice {
  case replaceable {
    replaceable .= [{ newValue }]
    // Complete payload replacement; may select payload +++ replacement.
  }
}
```

The binding must reach a type-side varying payload place through writable,
declaration-side varying access. A final payload place or readonly payload path
rejects complete reconstruction. No extra trailing-dot variant syntax exists.

## Presence and named access answer different questions

`?message` asks whether any alternative is active:

```zax
if ?message
  print("some named payload is present")
```

It does not prove which name is active.

Direct named access uses the variant's selection state:

```zax
textView : String readonly & = message.text
```

Four outcomes are distinguished:

1. If `text` is proved active, access succeeds and needs no runtime check.
2. If `text` is proved inactive, source is a compile-time error.
3. If the active name is not proved, access checks at runtime and panics when
   `text` is inactive.
4. If that registered check is disabled, the programmer promises `text` is
   active; violation has undefined consequences.

The panic category is `inactive-variant-access`, enabled by default. It applies
to reads, reference formation, ordinary assignment, compound operations, and
calls through a named alternative. It does not apply to `.=` or a switch route
that establishes the active name.

Exact panic-control source syntax remains future analysis-control work. Static
proof may always remove an enabled check, while a statically proved mismatch
remains an error even when the category is disabled.

## Payload references end with that payload

```zax
message.text .= "first"
oldText : String readonly & = message.text

message.text .= "second"
use(oldText) // error: the first text lifetime ended
```

The same invalidation occurs when:

- another alternative is selected;
- the wrapper is reset;
- the complete wrapper is replaced; or
- the wrapper is destroyed.

Address reuse does not preserve the old payload instance. A reference into an
active alternative remains tied to that exact conditional life path.

## Wrapper and payload qualifications are separate

The wrapper owns selection; the selected type owns payload behavior.

- wrapper `mutable` permits selection or presence to change during one wrapper
  lifetime;
- wrapper `immutable` keeps absence or the selected name stable for that
  wrapper lifetime;
- writable access is required to reset or use `.=`; and
- complete wrapper replacement additionally requires a varying place and
  declaration-side replacement authority.

Payload access carries the payload's own resolved qualifications:

```zax
MyChoice :: variant {
  stableText : String immutable
  workingText : String mutable
}
```

An authorized mutable/writable wrapper may end `stableText` and select another
payload. That operation ends a conditional path; it does not mutate the
immutable `String` through payload access.

A readonly path cannot reset or select:

```zax
reader : MyChoice readonly & = choice
reader.workingText .= "blocked" // error: wrapper path is readonly
```

## Copy, deep, move, and last

Whole-wrapper transfer preserves absence or the active name:

- `copy` copies the active payload and leaves the source unchanged;
- `deep` requires the active payload's exact independent-copy contract;
- `move` leaves the source present with one moved-from payload;
- `last` leaves the source present with one terminal but destruction-valid
  payload; and
- reset or ordinary wrapper destruction later ends that remaining payload.

```zax
MyOwnerChoice :: variant {
  owner : MyPayload * unique
  unavailable : MyUnavailable
}

owners : MyOwnerChoice
owners.owner .= makeOwner()

next : MyOwnerChoice = owners as move
// next.owner owns the transferred resource.
// owners remains present with a moved-from owner payload.

reset owners
```

The operation family must be viable for every alternative that the wrapper may
hold at that use. When flow proves one active name, source can operate on that
payload directly.

`last` never silently resets the source wrapper. Payload transfer and selection
state are separate. Moving a payload into another variant therefore leaves the
source active until reset or another lifecycle operation ends it.

General stance meaning and fallback belong to
[Zax transfer stances](transfer-stances.md). A case binding reached from a
by-value selector preserves that selector's projected offered stance but
performs no transfer merely by binding.

## Select alternatives with variant `switch`

When a `switch` selector has variant type, case entries use a dedicated
alternative-routing mode:

```zax
switch message {
  case text {
    print(text)
  }

  case number, alternateNumber bind selectedNumber {
    print(selectedNumber)
  }

  case ? bind other
    handleOtherPresent(other)

  case ! {
    print("absent")
  }
}
```

- `case text` resolves `text` in `MyMessage`'s alternative namespace and binds
  reference-shaped payload access under that same name.
- `bind` gives several alternatives one common body-local binding.
- a several-name case without `bind` routes control but introduces no payload
  local;
- the body is checked separately for every alternative that can select it;
- `case !` selects absence and introduces no payload binding.
- `case ? bind value` selects any remaining present alternative and specializes
  the body for every one.
- `default` remains positional fallback.

Specific names may appear before the present catch-all:

```zax
switch message {
  case text
    print("text:", text)

  case ? bind other
    print("another present payload:", other)

  case !
    print("absent")
}
```

Case bindings are aliases, not copies. They preserve origin, qualifications,
and the transfer stance projected from the retained selector.

Complete ordering, labels, direct entry, continuation, coverage, and diagnostics
are defined by
[switch, case, and default](switch.md#variant-alternative-selection).

## Coverage and partial selection

Variant selection checks two dimensions:

1. **Alternative coverage**: every declared name is handled by a named case or
   `case ?`.
2. **State coverage**: absence reaches `case !` or `default`.

`default` is fallback but does not silently count as explicit alternative
coverage. Deliberately omitting alternatives uses:

```zax
intent<partial-variant-selection>{
  switch message {
    case text
      print(text)
    default
      handleOtherState()
  }
}
```

The acknowledgement preserves the fallback behavior. It does not bind an
omitted payload or make the switch exhaustive under another meaning.

Adding an alternative makes a named-complete switch incomplete. A `case ?`
deliberately accepts future present alternatives when its body still specializes
successfully.

## Control-flow entry cannot manufacture a binding

A direct `goto` into a payload-bound clause is illegal because it would bypass
selection and binding.

A `continue case_label:` may retest the target route and enter only after it
establishes the binding. `next`, `break`, and outward transfer retain their
ordinary meanings.

Bare `case ?`, `case !`, `default`, and transfer-only clauses introduce no
payload binding. Direct entry can target an otherwise eligible unbound body, but
the path receives no fact from the bypassed test. A body may use only facts
common to every incoming path.

## Recursive and nested alternatives

Direct recursive payload storage has no finite extent:

```zax
MyRecursive :: variant {
  next : MyRecursive // error: infinitely recursive payload storage
}
```

Finite indirection is valid:

```zax
MyRecursive :: variant {
  end : MyEnd
  next : MyRecursive * unique
}
```

An optional, pointer, nested variant, or comparable payload keeps its own
semantic boundary. Variant selection never flattens nested absence, vacancy, or
selection state.

Complete generic variant formation remains future generic work. Every
instantiation must close to one concrete alternative set before maximum storage,
lifecycle families, and coverage are checked.

## Alternatives are not structural anchors

An alternative is a conditional payload path, not a statically resident member
path. It cannot be a compatibility anchor:

```zax
choice anchor .text // error: conditional alternative
```

A physical member whose complete value is a variant can be anchored as one
atomic wrapper:

```zax
container anchor .choice
```

That operation does not enter or select the active payload. Complete anchor
behavior belongs to
[structural shapes and compatibility](structural-shapes-and-compatibility.md#unions-and-variants-remain-semantic-leaves).

## Composition stops at the variant wrapper

Composition modifiers on a stored variant member apply to the wrapper value.
They do not enter whichever payload is active:

```zax
MyDetails :: type {
  label : String
}

MyPanel :: type {
  content : MyContentChoice
}

usePanel final : ()(panel : MyPanel readonly &) = {
  inspect(panel.content)
  inspect(panel.label) // error: no implicit delegation through the variant
}
```

`own`, `preferred`, `expose`, and outer casting cannot erase visible alternative
selection, absence, aliasing, lifetime, or runtime cost. Any future indirect
delegation facility must state those consequences explicitly.

## Costs and representation

A variant requires enough representation to distinguish absence from every
alternative and to store its largest payload with suitable alignment.

The language does not promise:

- a particular discriminant width or location;
- a separate tag rather than a valid niche;
- one payload offset;
- stable ABI or calling convention;
- raw relocatability; or
- one lowering strategy.

Programmer-visible costs include:

- discriminant or equivalent state;
- runtime checked named access when proof is unavailable;
- payload construction and destruction during `.=` and reset;
- per-alternative transfer dispatch;
- generated body specialization for polymorphic cases; and
- larger size or alignment when alternatives change.

## Diagnostics

Diagnostics should distinguish:

- absent access from a different active name;
- a compile-time-proved inactive name from runtime-checked uncertainty;
- disabled `inactive-variant-access` whose promise is known false;
- a packet with several alternatives;
- bare wrapper `.=` without a named alternative packet;
- ordinary `=` used where contained reconstruction `.=` is intended;
- wrapper replacement used where payload assignment is intended;
- aliasing into a payload that `.=` will end;
- reference use after reset or reselection;
- missing payload operation blocking whole-wrapper transfer;
- incomplete alternative or absence coverage;
- a direct control-flow entry that cannot establish a binding;
- a polymorphic case body failing for one named alternative;
- hidden varying storage declared after the alternative prologue;
- a custom lifecycle declaration that conflicts with protected wrapper
  behavior; and
- direct recursive payload storage.

## Source stability and maturity

Adding, removing, renaming, or retyping an alternative can change size,
alignment, transfer availability, switch coverage, specialization, and source
validity. Adding an alternative is intentionally visible to named-complete
switches; `case ?` is the explicit future-present catch-all.

This document defines current conceptual design, not formal grammar, a
generalized pattern system, ABI, reflection schema, discriminant layout,
concurrency protocol, or compiler implementation.
