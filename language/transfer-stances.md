# Zax transfer stances

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-visible `copy`, `deep`, `move`, and terminal-transfer intent; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | The transfer-stance mental model; declaration, type-alias overlay, and use-site stance; `copy`/`deep`/`move`/`last` meaning and fallback; value/reference and receiver behavior; source post-state; terminal intent; projection; common costs, diagnostics, and source stability |
| Does Not Own | Exact callable selection mechanics ([function invocation](function-invocation.md)); cohesive [exceptional result flow](except.md); composition publication and wrapper eligibility ([Zax composition](composition.md)); generated lifecycle signatures ([construction, replacement, and destruction](construction-and-destruction.md)); qualifier axes ([qualifiers](qualifiers.md)); optional wrapper cleanup ([optional values](optional-values.md)); complete [variant transfer](variants.md#copy-deep-move-and-last) or [union transfer](unions.md#whole-union-operations); [reference lifetime](lifetimes-and-references.md); or [pointer ownership and allocation](pointers-and-arenas.md) |
| Source / Provenance | Legacy function, pointer, casting, and constructor input, reconciled with current invocation, construction, qualifier, optional, operator, and documentation design |

## Why transfer stance exists

Start with ordinary-looking code:

```zax
a := b
c = d
```

It is reasonable to expect `a` to become its own value and `c` to receive the
value supplied by `d`. The source does not yet answer several important
questions:

- Does ordinary `copy` duplicate every resource or share some backing state?
- Does the destination need a recursively independent value?
- May exclusive ownership move out of the source?
- Must the source remain ordinarily usable?
- Is this the source's final meaningful use?
- Which allocation, copying, and destruction costs follow?

Zax does not answer those questions by inventing a separate assignment,
constructor, call, and operator spelling for every transfer policy. Instead, one
**transfer stance** describes how a complete source value is offered to a
consumer.

```zax
ordinary := source
independent := source as deep
transferred := source as move
terminallyTransferred := source as last
```

The most important rule is:

> `source as move` changes the stance offered for consumer selection. It does
> not transfer anything by itself.

The same is true of every `as <stance>` form. A constructor, parameter,
assignment, result slot, receiver-qualified function, or another selected
consumer performs any copy or transfer and establishes the resulting source
state.

## One stance on one complete value

Zax has four transfer stances:

| Stance | Ordinary purpose |
| --- | --- |
| `copy` | Preserve the source and use the type's ordinary duplication or sharing policy |
| `deep` | Preserve the source and request the type's independent-copy contract |
| `move` | Permit resource transfer while leaving one live moved-from source value |
| `last` | Permit terminal resource reuse when ordinary source preservation is no longer required |

A stance applies once to the complete source or receiver. It is not another
qualifier independently repeated at optional, pointer, reference, or member
layers:

```zax
source : MyValue

copied := source as copy
moved := source as move
```

Several complete restatements are legal. They apply left to right, so the final
one is offered to the consumer:

```zax
result := source as move as copy
// `as copy` is the final restatement, so result's consumer sees copy.
```

A malformed attempt to place two stance words in one phrase remains invalid:

```zax
result := source as move copy // error: not one recognized stance phrase
```

A type may contain values with their own declaration stances. Projection rules
decide which one is offered, but the type does not acquire a stance at every
indirection depth.

## Copy

`copy` is the ordinary omitted stance:

```zax
source : Document
snapshot := source
```

The source remains unchanged. `copy` does not necessarily mean recursively
independent storage. A type's `copy` contract may:

- duplicate inline state;
- retain shared backing;
- update reference counts;
- duplicate handles or references;
- allocate;
- use copy-on-write behavior; or
- invoke programmer-defined code.

The type must preserve its public ownership, qualification, and lifetime
contracts, but ordinary `copy` may deliberately share resources.

An explicit `copy` restatement is useful when a declaration normally offers
another stance or when source intent needs to be unambiguous:

```zax
resource : Resource move

preserved := resource as copy
```

## Deep

`deep` is for a type whose ordinary `copy` policy may share resources when the
program needs the type's recursively independent interpretation:

```zax
template : Project
editableProject := template as deep
```

A `deep` contract typically promises that later mutation or destruction through
one result will not affect resources that the other value requires. Types with
interned immutable data, identity-bearing resources, cycles, or external state
still define what meaningful `deep` independence means for them.

`deep` has no ordinary-`copy` fallback:

```zax
editableProject := template as deep
// error if Project supplies no compatible deep consumer
```

Silently using ordinary `copy` would discard the independence intent that made
`deep` necessary.

The compiler may not be able to prove every semantic property of arbitrary
programmer-defined `deep` code. Declaring the `deep` variant nevertheless makes a
programmer-facing contract.

## Move

`move` permits a selected consumer to transfer resources out of the source while
leaving one live source value:

```zax
buffer : Buffer
nextBuffer := buffer as move
```

If a `move` consumer is selected:

- `nextBuffer` becomes a complete valid destination;
- `buffer` remains one live `Buffer`;
- `buffer` satisfies the type's moved-from contract;
- every resource is transferred, retained, or dispositioned exactly once; and
- `buffer` is destroyed later by its original owner.

A type may promise broad moved-from usability or only operations such as
destruction and authorized replacement. The type's public contract must explain
which operations remain meaningful.

`move` is an offer, not a requirement to take ownership of a resource. A
selected `move` implementation may retain source resources or use copying when
that still satisfies its contract.

## Last

`last` exists for resources that are expensive to duplicate and useful to reuse
when a source is near the end of its useful work.

Consider a command that owns a private database while preparing a request:

```zax
postDatabaseCommand final : ()() = {
  command : DatabaseCommand
  command.preparePrivateDatabase()

  submit(command as last)
}
```

A `move` consumer must leave `command` in its moved-from state. That state may
still need its own database. A `last` consumer receives a final opportunity to
take ownership of the private database because `command` will reach ordinary
destruction when the function exits.

`last` does not schedule or cause that outer lifetime to end:

- the original declaration still owns its storage;
- its destructor still runs at the ordinary lifetime boundary;
- enough valid state remains for that destructor to run once; and
- the old source no longer releases any resource whose ownership moved to the
  selected consumer.

The source still exists, but resources that ordinary methods expect may now
belong elsewhere. Continued use is therefore suspicious:

```zax
postAndReport final : ()() = {
  command : DatabaseCommand
  command.preparePrivateDatabase()

  submit(command as last)
  printStatistics(command)
  // error: printStatistics may require resources transferred by submit
}
```

A deliberate operation that is defined for the remaining state uses an
[intent acknowledgement](intent-acknowledgements.md):

```zax
postAndRelease final : ()() = {
  command : DatabaseCommand
  command.preparePrivateDatabase()

  submit(command as last)

  intent<terminal-source-reuse>{
    command.releaseRemainingHandle()
  }
}
```

The acknowledgement confirms intent. It cannot make an operation valid when the
type's terminal contract does not support it.

### When `move` and `last` differ

`move` must preserve the type's moved-from contract. `last` only needs to
preserve enough state for destruction and explicitly defined terminal-state
operations.

For many types, the two implementations are the same. An empty buffer, null
handle, or zero-element collection may already be both a useful moved-from state
and enough state for destruction. Such a type can omit a separate `last`
implementation and use fallback to `move`.

A specialized `last` implementation matters only when the weaker terminal
requirement allows more resource reuse.

#### Private database connection

Suppose every ordinarily live command needs its own private database connection:

- `move` may transfer other resources but must retain or recreate a connection
  for the moved-from command;
- `last` may transfer the connection because the command only needs to reach
  destruction.

#### Reusable backing allocation

A moved-from buffer may promise that it remains immediately reusable:

- `move` transfers the payload but retains enough backing allocation for future
  writes;
- `last` may transfer the payload and backing allocation.

#### Registration or identity token

An ordinarily live service may need to remain registered:

- `move` preserves or recreates registration for the moved-from source;
- `last` may transfer or retire the registration because no ordinary later
  service operation is expected.

#### Self-referential representation

A moved-from structure may need to rebuild internal pointers so ordinary methods
remain valid. A terminal source may avoid that repair cost because only
destruction remains.

A type may therefore:

- provide `move` and let `last` fall back to it when moved-from state is already
  minimal;
- provide a specialized `last` but no `move` when maintaining a continuing
  moved-from invariant is too expensive;
- or provide neither when `copy` already supplies the same useful behavior.

## Fallback

An offered stance may accept a consumer that changes the source less:

| Offered stance | Preferred accepted consumer |
| --- | --- |
| `copy` | `copy` |
| `deep` | `deep`; otherwise error |
| `move` | `move`, then `copy` |
| `last` | `last`, then `move`, then `copy` |

The accepted consumer determines source state:

| Accepted stance | Source afterward |
| --- | --- |
| `copy` | Unchanged |
| `deep` | Unchanged |
| `move` | Live and moved-from |
| `last` | Live and destruction-valid in terminal state |

Destruction-valid means that enough correct source state remains for its
destructor to run once without releasing resources that now belong to another
value.

For example, `Archive` initially has only a `copy` constructor:

```zax
Archive :: type {
  +++ final : ()(
    source : Archive readonly & copy
  ) = {
    // ...
  }
}

source : Archive
first : Archive = source as move
// No `move` constructor exists, so `copy` is accepted as fallback.
// source remains unchanged.
```

If the type later supplies an exact `move` variant, the same offer selects it:

```zax
Archive :: type {
  +++ final : ()(
    source : Archive readonly & copy
  ) = {
    // ...
  }

  +++ final : ()(
    source : Archive mutable writable & move
  ) = {
    // ...
  }
}

source : Archive
second : Archive = source as move
// The exact `move` variant is preferred.
// source becomes moved-from.
```

Fallback is coherent only when stance variants describe one logical domain
operation and destination outcome. Variants may differ in allocation, source
preservation, resource reuse, destruction work, and cost. They must not use
stance variation to perform unrelated business behavior.

## Declaring accepted stances

The usual transfer contracts use readonly references for `copy` and `deep` and
mutable/writable references for `move` and `last`:

```zax
+++ final : ()(
  source : Archive readonly & copy
) = {
  // Copy construction.
}

+++ final : ()(
  source : Archive readonly & deep
) = {
  // Independent construction.
}

+++ final : ()(
  source : Archive mutable writable & move
) = {
  // May transfer resources from source.
}

+++ final : ()(
  source : Archive mutable writable & last
) = {
  // May take ownership of resources before the source is destroyed.
}
```

`move` and `last` normally need mutable state through writable access. Taking
ownership of a resource must both transfer that ownership to the destination and
update the old source so it no longer treats the resource as available.
Unusual readonly `move` or `last` contracts remain legal when their behavior is
coherent, such as manipulating external state without changing the represented
source.

These examples use omitted mutability and place qualifiers as shorthand. Each
omission resolves to one concrete default shape; it does not make the
declaration generic over mutable/immutable or final/varying.

## By value and by reference

By-value and reference-shaped parameters answer different ownership questions.
Both are legal and may coexist.

### By-value move

```zax
prepareForSend final : ()(
  input : Message move
) = {
  send(input.payload)
}
```

By value guarantees that a constructor establishes `input` before body entry.
For this `move` form:

1. the caller's offered `move` stance selects this parameter contract;
2. because the parameter is by value, a `copy` construction establishes the
   independent `input`;
3. the caller's original message remains separate;
4. the completed parameter has `move` declaration stance;
5. later work on `input` may move its copied resources instead of repeatedly
   copying them; and
6. the remaining local state is destroyed when the call completes.

It is a **copy-in barrier followed by owned `move` processing**. A move-only
caller value cannot use this form when the preliminary `copy` is unavailable.

### Reference move

```zax
prepareForSend final : ()(
  input : Message mutable writable & move
) = {
  send(input.payload as move)
}
```

This form binds directly to caller-owned storage. The selected implementation
may change the caller's source state. Because `input` is a named reference,
ordinary nested uses return to `copy` intent. Otherwise an earlier helper could
move resources before the function reaches the intended transfer:

```zax
log(input)                  // preserves input under `copy`
send(input.payload as move) // transfers the payload deliberately here
```

If `log(input)` inherited `move`, it could select a move-aware logger and leave
`input` moved-from before `send`. Explicit restatement makes the actual resource
transfer visible. The same rule applies to `last`.

The copy-default rule also prevents terminal authority from escaping
implicitly:

```zax
consume final : ()(
  input : Message mutable writable & last
) = {
  stored : Message mutable writable & last = input
  // error: named input ordinarily offers copy

  explicit :
    Message mutable writable & last = (input as last)
}
```

The explicit form constructs another reference bound to the same instance place
and carrying `last` declaration stance. It remains subject to independent
reference-lifetime proof, and ordinary later use again offers `copy`.

### By-value deep

A by-value `deep` parameter is deep-constructed first and retains `deep` inside
the body:

```zax
prepareTemplate final : ()(
  input : Document deep
) = {
  archive(input.history) // history ordinarily offers deep
}
```

This may perform more independent copying than intended. A readonly-reference
`deep` contract is usually clearer because it avoids first creating a complete
`deep` parameter and lets the body choose how to establish each destination
resource.

### Choosing value or reference

For a writable source and otherwise equal `move` or `last` candidates, the normal
mutable/writable reference form is better than the by-value form:

```zax
prepareForSend(message as move)
```

A safe readonly restatement can make that reference form unavailable and select
copy-in processing:

```zax
prepareForSend((message as readonly) as move)
```

If another readonly-reference variant also exists, neither choice may be better
overall: one may avoid copying while the other preserves caller-owned storage.
The later selection model calls those candidates **incomparable** and reports
ambiguity rather than guessing.

Complete viability and partial-order comparison belong to
[function invocation](function-invocation.md).

### Pointer ownership transitions

Pointer ownership uses the same stance vocabulary while the destination pointer
type states the requested ownership result.

```zax
shared : MyValue * strong = prepared as last
```

Here `prepared` must be `MyValue * unique shareable`. The transfer activates its
reserved control block and vacates the unique source.

A declaration-attached raw allocation carries disposition responsibility in its
declaration rather than in the raw pointer type:

```zax
scheduled : MyValue * = @
owner : MyValue * unique = scheduled as last
```

An accepted transfer leaves `scheduled` vacant. Its declaration cleanup
remains scheduled but later has no allocation to disposition. The `unique`
destination becomes the allocation owner.

A scheduled raw destination may adopt a `unique` owner or another scheduled raw
allocation:

```zax
scheduled = uniqueOwner as last
scheduled = otherScheduled as last
```

It dispositions its previous allocation before adoption. The accepted source
becomes vacant.

An ordinary raw destination cannot retain either ownership relationship:

```zax
raw : MyValue * = uniqueOwner as last
// requires another proved disposition owner or narrow unsafe responsibility
```

This deliberately opens the allocation. A raw source may enter scheduled or
managed ownership only when analysis proves the allocation root, provenance, and
sole disposition authority.

The reverse claim is conditional:

```zax
owner : MyValue * unique = shared as last
```

It succeeds only when the source is the sole strong allocation-root owner and no
weak observer remains. Failure constructs an empty `unique`; the terminal strong
source retains its ownership until ordinary destruction.

Weak-to-strong acquisition is `copy`:

```zax
owner : MyValue * strong = observer
```

This copies participation in a still-live ownership relationship rather than
copying the pointee. Failure produces an empty strong pointer.

Complete pointer forms, control blocks, anchoring, atomicity, arenas, and
failure behavior are defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md).

## Declaration stance

A declaration may establish its ordinary future stance:

```zax
template : Project deep
resource : SingleOwnedResource move
```

Ordinary value transfer from `template` offers `deep`. Ordinary transfer from
`resource` offers `move`. Reference-only inspection may still bind a reference
without copying or moving the value:

```zax
inspect(resource)
transfer(resource)
```

A use-site restatement overrides the declaration for one consumer:

```zax
snapshot := resource as copy
```

An omitted declaration stance resolves to `copy`.

### Single-owner resources

A type that represents exclusive ownership may prohibit `copy` construction and
assignment because two values cannot own the same resource:

```zax
SingleOwnedResource :: type {
  +++ final : ()(
    source : SingleOwnedResource readonly & copy
  ) = forbidden

  // Exact qualifier-complete generated assignment shapes are forbidden
  // separately as needed.
}
```

Reference-shaped `move` and `last` remain meaningful. They transfer ownership
from one value to another without producing two owners. A by-value `move` form
may be unavailable because its preliminary `copy` cannot be constructed.
Neither `move` nor `last` needs to be prohibited merely because ownership is
exclusive.

### Initialization is independent

The source expression controls how a declaration is initialized. The
destination's declaration stance becomes active after construction:

```zax
makeResource final : (
  result : Resource move
)() = {
  // ...
}

stored : Resource copy = makeResource()
```

The result from `makeResource()` offers `move` while constructing `stored`.
Afterward, `stored` ordinarily offers `copy`.

An input declaration shows the opposite direction:

```zax
consume final : ()(
  input : Resource move
) = {
  // input offers `move` after its by-value construction completes.
}

consume(resource as move)
```

The caller's explicit `move` stance selects this parameter. By-value binding
still copies the caller's value to establish `input`. Once the parameter exists,
its declared `move` stance controls later uses in the body.

An inferred declaration may adopt both the concrete value type and produced
stance. Complete reference and pointer shape inference remains future
declaration design.

### Type aliases may overlay declaration stance

A concrete type alias may supply or replace the declaration stance inherited
from another alias:

```zax
DeepView :: alias type Payload readonly & deep
CopyView :: alias type DeepView copy
```

`CopyView` has the same canonical `Payload` identity and reference shape while
requesting `copy` for declarations that use it. `copy` replaces inherited
`deep`.

This is profile resolution, not transfer. No value is copied or consumed while
the alias is declared or expanded. The actual source must still satisfy the
resolved declaration and consumer contract. A use-site restatement remains
strongest for one consumer.

The general overlay order is defined by
[declarations and bindings](declarations-and-bindings.md#exact-aliases-and-property-overlays).

## Projection and aliases

When member access starts from a by-value declaration, the declaration's stance
becomes the default for any member that has no explicit stance:

```zax
package : Package move

ship(package.label) // label offers move when its declaration has no stance
```

An explicit member stance wins:

```zax
Package :: type {
  label : Label copy
}

package : Package move
ship(package.label) // copy
```

A use-site restatement is strongest:

```zax
ship(package.label as last)
```

The precedence is:

1. use-site restatement;
2. explicit member declaration stance;
3. stance inherited from the containing by-value declaration;
4. implicit `copy`.

A reference is not another by-value declaration. It aliases the same place but
has its own declaration stance, so creating a reference does not silently carry
destructive intent from the declaration used to initialize it:

```zax
owner : Buffer move
view : Buffer mutable writable & = owner

inspect(view)       // copy/default
consume(view as move)
```

The alias must still preserve the referent's actual mutability, place, and access
qualifications.

### Compatible whole values and anchored regions

A whole-source structural recast preserves its offered transfer stance.

An anchor can select one interior region while excluding surrounding state:

```zax
PayloadSummary :: type {
  payloadSize : U32
  payloadChecksum : U32
}

Packet :: type {
  header : Integer
  payloadSize : U32
  payloadChecksum : U32
  trailer : Integer
}

consumePayloadSummary final : ()(
  payload : PayloadSummary readonly & copy
) = {
}

packet : Packet last compatible shape
consumePayloadSummary(packet) // error: complete Packet is not PayloadSummary
consumePayloadSummary(packet.payloadSize) // error: payloadSize is only U32
consumePayloadSummary(packet anchor .payloadSize)
```

The anchor starts at `payloadSize` and spans the sibling
`payloadChecksum` member because the consumer already requires one complete
`PayloadSummary`. No `PayloadSummary` member exists inside `Packet`;
`packet.payloadSize` alone cannot supply it.

The anchored view excludes `header` and `trailer`. It therefore receives
ordinary `copy` rather than inheriting the complete `Packet`'s `last`:
terminally consuming the middle region alone might leave excluded state unable
to complete `Packet` destruction.

An explicit `(packet anchor .payloadSize) as last` request is viable only when the
applicable source contract establishes that complete-source terminal guarantee.
A result declaration may combine `last` with one default anchor to publish that
contract. A caller-selected different anchor does not inherit it.

If conversion selects `copy`, the region remains `copy` even when the outer
declaration ordinarily offers `last`. Complete compatibility and anchor behavior
is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#transfer-stance-and-anchored-regions).

### Explicit composition wrappers

A composition `via` or `= existing` declaration may accept a complete outer
value and pass one contained member to the selected operation:

```zax
Tank :: type {
  fuel preferred : Fuel

  refill final : ()(source : Tank) = via fuel.refill
}
```

The wrapper first establishes its by-value `source` parameter under the visible
outer stance. Automatic mapping to `source.fuel` is available because `fuel` is
`preferred`; `own` alone would not create an expected-input route. A written
body may always pass `source.fuel` explicitly. The selected member is then
offered according to the outer input shape:

| Outer input | Offer made for the selected member |
| --- | --- |
| By-value `copy`, `move`, or `last` | `last` |
| By-value `deep` | `deep` |
| Reference-shaped input | The caller's exact authorization |

Offering the member as `last` tells the contained operation that this is its
final opportunity to reuse resources from the wrapper's temporary outer value.
Normal fallback still applies:

```text
last -> move -> copy
```

The operation need not accept that opportunity. A `copy` wrapper can therefore
perform one copy to establish the outer parameter and another copy when the
contained operation consumes the member.

An outer `deep` parameter remains `deep`; it is never changed to `last`. A
reference-shaped `move` likewise remains `move` rather than gaining stronger
authority.

The wrapper destroys the remaining outer value in a valid terminal-member state.
Complete route eligibility and the contained-operation result rules belong to
[Zax composition](composition.md#forwarding-values-stances-and-results).
A custom destructor that cannot accept that terminal-member state makes the
mechanical mapping unavailable; the container must use a written wrapper that
performs the required policy explicitly.

## Receiver stance

A type-defined function has an implicit input: the current instance on which it
acts. The value before a member call is that **receiver operand**. A programmer
may want different implementations when the receiver requests independent
copying, permits resource transfer, or offers final resource reuse, so receiver
stance participates in selection:

```zax
document : Document deep
document.publish()

(document as copy).publish()
```

The first call gets `deep` from `document`'s declaration and may select a
`deep`-receiver `publish`. The second restates `copy` for that one receiver use.
Parameter and result stances occupy separate callable slots and do not change.

A type may declare exact receiver variants:

```zax
Document :: type {
  publish final : ()() deep = {
    _.content.prepare()
  }

  publish final : ()() copy = {
    _.content.prepare()
  }
}
```

Receiver stance is independent from parameter and result stances.

### Receiverless `once` calls

A receiverless type call has no source declaration from which to obtain a
stance. It synthesizes the containing type's Nothing receiver under `copy`
before callable selection:

```zax
MyResource :: type {
  inspect final once : ()() = {
  }

  consume final once : ()() last = {
  }
}

MyResource.inspect()
MyResource.consume() // error: receiverless copy cannot select exact last
```

An instance-qualified call retains its actual receiver stance:

```zax
(resource as last).consume()
```

No runtime rule turns `last` into `copy`. The invocation route determines the
offered stance statically, and aliases or bound callable values preserve
that route. Complete receiverless and Nothing-instance behavior is defined by
[Zax Nothing instances](nothing-instances.md#receiver-stance-is-statically-known).

### Compiler-created temporary receivers

A directly constructed compiler-managed unnamed by-value temporary has a
structurally known end: its lifetime completes after its one complete consumer.
That temporary inherently offers `last`. This is a stable property of the source
form, not liveness analysis applied to a named declaration.

```zax
Source :: type {
  get final : (
    result : Payload copy
  )() copy = {
    // Preserve a named receiver and produce a copy-stanced result.
  }

  get final : (
    result : Payload last
  )() last = {
    // Reuse resources from a receiver whose temporary lifetime is ending.
  }
}

fromTemporary := (: Source).get()

source : Source
fromNamed := source.get()
```

The anonymous `(: Source)` receiver offers `last` without a programmer-written
restatement and may select the `last` receiver variant. The named `source` uses
its declaration stance, implicitly `copy` here.

The selected receiver variant can change how it produces its result. The
`last`-receiver `get` may transfer receiver resources into a `last` result, while
the `copy` receiver preserves its source and returns a `copy` result.

A programmer may deliberately restate another stance on the temporary:

```zax
fromTemporaryCopy := ((: Source) as copy).get()
```

### Unstanced receiver fallback

An unstanced receiver declaration is the fallback for every receiver stance:

```zax
Document :: type {
  publish final : ()() = {
    _.content.prepare()
  }
}
```

Its body uses `copy` stance. It does not fork its implementation according to
the stance that caused fallback.

An exact `copy` receiver and an unstanced fallback are distinct:

```zax
Document :: type {
  publish final : ()() = {
    // Fallback for any receiver stance.
  }

  publish final : ()() copy = {
    // Exact copy receiver.
  }
}
```

An implicit or explicit `copy` receiver selects the exact `copy` variant.
`deep`, `move`, or `last` may use the unstanced fallback:

```zax
ordinary : Document
deepDocument : Document deep

ordinary.publish()     // exact `copy` receiver
deepDocument.publish() // unstanced fallback if no exact `deep` version exists
```

Receiver selection does not use the source fallback ladder. An exact `move`
receiver does not service `last`, and an exact `last` receiver does not service
`move`.

### Stance inside a receiver body

| Selected receiver declaration | Ordinary nested receiver use |
| --- | --- |
| `copy` | `copy` |
| `deep` | `deep` through unstanced projections |
| `move` | `copy` unless explicitly renewed |
| `last` | `copy` unless explicitly renewed |
| Unstanced fallback | `copy` |

`move` and `last` select the current implementation, but each nested call is
another consumer:

```zax
Document :: type {
  publish final : ()() last = {
    _.inspect()
    (_ as last).finishPublishing()
  }
}
```

This prevents an innocent nested helper from taking resources before the body
reaches its intended transfer site and matches named `move`/`last` references.

## Results and forwarding

A result declaration stance becomes active when its slot is constructed and is
also the produced result stance:

```zax
makeMessage final : (
  result : Message move
)() = {
  result.+++(source as copy)
  inspect(result)
}
```

The `copy` restatement controls construction of `result`. Once constructed,
`result` ordinarily offers `move`, including after return.

For a callable declaration with a body, that internal stance behavior is minted
once with the implementation. A compatible visible prototype may present a
different outward result stance when call-boundary mapping can satisfy it, but it
does not reprocess the body or change any internal operation selected from
`result`.

For example, an implementation minted with a scheduled raw `last` result may be
exposed as explicit `copy` for deliberately borrowed caller use. The minted body
retains its original stance and scheduled replacement behavior; the alias
changes only outward mapping. Caller lifetime analysis still rejects a copied
raw pointer that would outlive producer cleanup.

Forwarding crosses two declarations:

- the producer result stance controls transfer into the forwarding result slot;
- the forwarding result declaration controls what its caller receives.

Result construction, mapping, and completion are defined by
[function invocation](function-invocation.md).

Exceptional handling and forwarding use exactly this rule:

```zax
value := operation() catch failure: localFailure: {
  report(localFailure)
  return
}

value := operation() except failure: outerFailure:
```

No implicit `move` or `last` is added. The producer exceptional result stance
controls transfer into `localFailure` or `outerFailure`; an outer exceptional
result declaration then controls what its caller receives. Move-only and owning
payloads therefore state a viable result stance in their callable prototypes.

Conditional elision may construct directly in the handler or outer exceptional
destination. Provisional cleanup responsibility transfers outward only after
that mapping succeeds. Complete exceptional behavior is taught by
[Zax exceptional result flow](except.md#transfer-ownership-and-references).

### Terminal opportunity when mapping a result

A function result exposes the stance written in its producer prototype.
Omission still means implicit `copy`; a caller does not silently rewrite that
public result contract to `last`.

An owned by-value result slot is nevertheless temporary source storage. At the
downstream mapping boundary, the compiler can see both:

- that mapping is structurally the result slot's final consumer; and
- whether offering `last` would materially change the accepted destination
  contract.

```zax
consume final : ()(
  input : Payload readonly & copy
) = {
}

consume final : ()(
  input : Payload mutable writable & last
) = {
}

makePayload final : (
  result : Payload
)() = {
  // Implicit `copy` result stance.
}

consume(makePayload())
// intent error: this by-value result could select the `last` consumer,
// but the producer result left its stance implicit
```

The producer can declare an API-wide result stance:

```zax
makePayload final : (
  result : Payload last
)() = {
}
```

The caller can restate one use:

```zax
consume(makePayload() as last)
```

Or the producer can explicitly preserve `copy`:

```zax
makePayload final : (
  result : Payload copy
)() = {
}
```

No intent error is needed when the stance is explicit, no `move`/`last`
consumer is available, `last` would accept the same `copy` contract with the
same source outcome, or the result is discarded.

Each slot of a multiple-result producer is considered independently.

Reference results are excluded. A reference result owns only an access path, so
destruction of its result slot does not prove the referent is terminal. Pointer
ownership follows the selected pointer or declaration contract; transfer of an
ordinary pointer value does not automatically make its pointee terminal. A
scheduled raw result declared with `last` is an explicit exception because its
result declaration owns disposition responsibility. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md).

## Terminal opportunity must be explicit

Zax does not silently change an implicit `copy` or explicit `move` into `last` merely
because analysis proves that the source is near its final use. Selecting another
last-aware body is language behavior, not optimization.

When a proven terminal opportunity could materially change the accepted
consumer, implicit source is an intent error:

```zax
forward(resource)
// error: state copy, move, last, or deep intent explicitly
```

The programmer chooses:

```zax
forward(resource as last)
forward(resource as move)
forward(resource as copy)
forward(resource as deep)
```

or deliberately retains implicit behavior:

```zax
intent<implicit-stance-at-terminal-use>{
  forward(resource)
}
```

No error is needed when `last` would accept the same contract and produce the
same source post-state. For example, a copy-only observer has no terminal-aware
alternative:

```zax
inspect final : ()(
  value : Record readonly & copy
) = {
}

inspect(record)
// No `move` or `last` consumer exists, so decoration would change nothing.
```

An explicit declaration stance is not upgraded:

```zax
resource : Resource move
forward(resource) // remains a move offer
```

Improved analysis may discover more intent errors, but it must not silently
select a different user body. Destruction does not count as ordinary later use.

## Callable values and captures

Capture construction and later callable transfer are different consumers:

```zax
callback := [[ source as last: captured ]] ()() bound strong {
  use(captured)
}
```

`source as last` controls construction of `captured`. That capture expression
runs once. Later transfer of `callback` never reevaluates or reconstructs it:

- `copy` copies the callable's receiver/lifetime relationship;
- a borrowed copy remains a borrow of the same receiver;
- a strong copy participates in the same receiver lifetime;
- a weak copy adds another observation;
- unique ownership cannot be copied;
- `move` and `last` transfer the applicable relationship and leave the source
  callable initialized but unavailable without releasing the moved
  relationship; and
- callable `deep` is undefined.

An accepted `move` or `last` inside a lambda body changes the captured value's
state. The callable remains live, so repeated invocation is valid only when
later operations accept the capture's moved-from or terminal state. Static
analysis diagnoses invalid repeated use. Complete behavior is defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#callable-transfer-reset-and-recursion).

## Optional values

Optional values have type-specific phrase adapters in addition to generic source
restatement:

```zax
source as last // generic `last` stance on the optional value
last source    // optional adapter with scheduled boxed cleanup
```

When a consumer accepts `last source`, the optional:

- offers `last` to a present payload;
- immediately enters terminal source state for analysis;
- keeps required wrapper and payload state live through the complete consumer;
- removes any remaining boxed value when that consumer completes; and
- remains as an absent wrapper.

The `move source` adapter forwards `move` inward without scheduling absence.

The complete wrapper, nested-state, cleanup, and optional swap behavior is
defined by [Zax optional values](optional-values.md).

## Variants and unions

A whole variant preserves absence or its active name while forwarding the
accepted stance to the payload:

- `copy` leaves the source unchanged;
- `deep` requires the exact payload-independent-copy contract;
- `move` leaves the source present with a moved-from payload; and
- `last` leaves the source present with a terminal but destruction-valid
  payload.

`last` does not silently reset the wrapper. Reset, `.=` selection, complete
wrapper replacement, or ordinary destruction later ends the source payload.
Complete behavior belongs to
[Zax variants](variants.md#copy-deep-move-and-last).

A case binding is reference-shaped projection rather than a copy. It preserves
the stance offered through the retained variant selector, and binding alone
transfers nothing:

```zax
choice : MyChoice move

switch choice {
  case owner
    consume(owner) // offers the projected move stance
}
```

The selected consumer determines source state normally.

An unmanaged union owns no transferable payload resources. Whole-union `copy`
copies every backing bit. `move` and `last` use their normal fallback to that
copy and leave the source unchanged. `deep` remains unavailable because an
unsafe union may contain passive raw pointers or similar representations whose
copied bits do not establish independent resources. See
[Zax unions](unions.md#whole-union-operations).

## Arrays and slices

Copying an owning array creates another array with its own element places and
backing storage. Each element performs its ordinary `copy`. `deep` does the
same at the array level but requires every element to support its exact
independent-copy contract; it never falls back to ordinary copy.

An array using inline storage cannot hand its enclosing bytes to another value,
so `move` and `last` transfer its elements one by one. A provider-backed array,
including a fixed-length one, may hand over its strong provider participation
and unique backing handle when the destination accepts them.

`move` must leave the source as a valid live moved-from array. `last` can leave
less behind because the source only needs to remain safe for its final
destruction.

Copying or moving a slice changes only its window description. It never
implicitly copies or moves the array elements viewed through that slice. A
storage-assisted consuming range transfer must be rooted in the owning array;
`slice as last` cannot transfer a handle the slice does not own.

Array joining accepts ordinary stance restatement:

```zax
combined := left <+> right
reused := left as last <+> right
```

With no explicit destination provider, `copy` or `deep` may retain the same
shared provider while requesting another unique region. `move` or `last` may
transfer the existing unique handle under their source-state rules.

The selected array operation decides whether provider/backing storage can be
reused, which elements must be copied or transferred, and which result size is
produced. Complete behavior is explained by
[Zax arrays and slices](arrays-and-slices.md#copy-deep-move-and-last).

## Qualifier promises remain exact

Transfer stance is not a fourth per-layer qualifier axis. Consumer declarations
still preserve three independent qualifier promises:

| Axis | Meaning |
| --- | --- |
| `mutable` / `immutable` | `immutable` promises that the current value's contents do not change through ordinary safe behavior; `mutable` makes no such promise |
| `final` / `varying` | `final` promises that the place continues to hold the same value lifetime; `varying` permits another lifetime in that place |
| `writable` / `readonly` | `readonly` promises that this access path performs no change; `writable` may perform an otherwise permitted change |

For example:

```zax
source : Buffer mutable readonly final & move
```

describes:

- a mutable current `Buffer` value;
- readonly access through this path;
- a final referenced place; and
- an offered `move` stance.

`readonly` does not erase mutable/immutable or final/varying. Every ordinary
prototype resolves one complete concrete shape:

```zax
consume final : ()(
  source : Buffer mutable readonly final & copy
) = {
}

consume final : ()(
  source : Buffer mutable readonly varying & copy
) = {
}
```

These are distinct declarations. Ordinary omission applies defaults; it does
not make a function implicitly generic or branch its body according to the
caller.

Future explicit qualifier generics may let one source declaration produce
demanded exact specializations. That future mechanism does not erase concrete
prototype identity.

Complete qualifier behavior is defined by [Zax qualifiers](qualifiers.md).

## Generated operations

Generated `copy` is the conservative baseline:

- the compiler exposes exact qualifier-complete `copy` variants for supported self
  shapes;
- a matching body, `default`, `existing`, bodyless declaration, or `forbidden`
  applies only to the exact generated shape;
- `deep`, `move`, and `last` are not generally generated;
- `move` and `last` use their ordinary fallback to `copy` when no stronger variant
  exists;
- `deep` remains unavailable without an exact `deep` contract.

An incorrect attempted replacement may coexist with generated behavior and
create a use-site ambiguity. Diagnostics should show the exact generated shape
that did not match.

Generated signatures, assignment association, reconstructive replacement, and
destruction are defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md).

## Aliasing and resource disposition

Same-object source is not categorically invalid:

```zax
value = value         // ordinary in-lifetime assignment
value .= value        // complete reconstructive replacement
value .= value as move
value .= value as last
```

The selected operation owns its same-object behavior. A numeric operation may be
harmless while a resource operation may have conflicting destination and source
postconditions.

Interior aliases create additional pressure:

```zax
owner .= owner.payload as move
```

A replacement cannot end `owner.payload` and then continue using a reference to
that ended lifetime. A custom replacement may transfer the member before
dispositioning the remaining old state.

The compiler diagnoses conflicts it can prove. Reference-origin and successor
boundaries are defined by
[lifetimes and references](lifetimes-and-references.md); the reusable proof and
unsafe model is defined by [safety and analysis](safety-and-analysis.md).

Every successful transfer or replacement must disposition each source resource,
old destination resource, member lifetime, result slot, and temporary exactly
once.

## Costs

No stance promises constant time, zero allocation, or resource theft.

Important visible costs include:

- ordinary `copy` may share, retain, allocate, or invoke user code;
- `deep` may traverse and allocate recursively;
- a by-value parameter may construct a complete value before the body performs
  more transfer work;
- reference `move`/`last` may change caller-owned state directly;
- `move` and `last` fallback may pay ordinary `copy` cost;
- generated structural `copy` performs member operations;
- assignment or replacement dispositions the old destination;
- optional `last` performs cleanup at consumer completion;
- capture may allocate or enlarge callable state;
- and lifetime, alias, generic-specialization, and terminal-use analysis add
  compile-time cost.

The type's operation family and documentation own its concrete cost model.

## Diagnostics

Diagnostics should identify the deciding contract, including:

- unavailable `deep` behavior with no fallback;
- accepted `move`/`last` fallback and resulting source state;
- value/reference ambiguity;
- the exact mutability, place, access, or stance mismatch;
- terminal opportunity requiring explicit intent;
- terminal-source reuse;
- a source or result consumed more than once;
- exceptional handler or forwarding mapping whose source stance cannot satisfy
  its destination;
- an exact generated declaration that failed to match;
- a member path blocking generated transfer;
- unsupported same-object behavior;
- a possible interior alias whose lifetime would end;
- invalid intent or unsafe acknowledgement;
- incomplete result, member, or replacement state;
- and zero or multiple resource disposition.

For a nested generated failure, the diagnostic should show the requested outer
contract, resolved qualifier path, and first missing, ambiguous, or unavailable
member operation.

## Source stability

- Explicit declaration and use-site stances are never silently upgraded.
- Improved terminal analysis reports intent errors rather than selecting another
  user body.
- Adding a stance variant may create a loud ambiguity but never wins by
  declaration, import, source, or discovery order.
- Exceptional handling and forwarding never gain a stronger stance merely
  because the source result is temporary or the outcome exits a function.
- Generated availability is exact-shape and demand-driven.
- Formatters and source-preserving tools retain every stance and intent
  acknowledgement.
- Optimization and implementation deduplication preserve selected contracts,
  source effects, callbacks, result shape, and destruction order.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar,
compatibility contract, conformance specification, or compiler mapping.

Focused future work remains for:

- detailed cross-axis callable preference and generic-specialization comparison;
- qualifier-generic syntax and constraints;
- detailed pointer provenance and casting beyond the current ownership model;
- moved-from and terminal operation eligibility;
- async suspension and cancellation;
- cross-thread value preparation and whether it is a stance, result capability,
  or separate contract;
- mutable/immutable representation-indexed families;
- formal diagnostics and analysis contracts;
- complete general swap mechanics; and
- formal grammar, reflection, ABI, and implementation.
