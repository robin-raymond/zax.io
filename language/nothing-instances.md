# Zax Nothing instances

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers using pointers, function values, or type-owned `once` behavior |
| Applies To | Programmer-facing Nothing instances, pointer vacancy, unavailable function values, receiverless type calls, compiler-provided/trapping/custom Nothing policies, and related access failures; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | The cohesive Nothing-instance mental model; absence of a universal `Nothing` type; compiler-prepared, trapping, and custom Nothing policies; prepared member behavior; receiverless presence; policy directives; shared-backing freedom; access boundaries; costs and diagnostics |
| Does Not Own | Pointer ownership and allocation ([pointers and arenas](pointers-and-arenas.md)); optional boxed lifetimes ([optional values](optional-values.md)); general invocation and callable selection ([function invocation](function-invocation.md)); ordinary construction and destruction ([construction and destruction](construction-and-destruction.md)); complete reference lifetime ([lifetimes and references](lifetimes-and-references.md)); exact operator forms ([operator catalog](operator-catalog.md)); or global/`once` initialization order |
| Source / Provenance | Legacy Nothing design reconciled with current pointer, optional, function, lifecycle, lifetime, safety, and module design |

## Four states that should not be confused

Start with four declarations and calls:

```zax
MyValue :: type {
  inspect final once : ()() = {
  }
}

pointer : MyValue *
optional : MyValue?
callback : ()()

MyValue.inspect()
```

They do not all contain one universal value:

- `pointer` is a live pointer whose value is **vacant**. It semantically targets
  `MyValue`'s Nothing instance, and `?pointer` is false.
- `optional` is a live absent wrapper. It contains no boxed `MyValue` lifetime.
- `callback` is a live but unavailable function value. `?callback` is false,
  and an unguarded call diagnoses or panics.
- `MyValue.inspect()` is a **receiverless type call** to a `once bound`
  function. Its `_` identifies `MyValue`'s Nothing instance.

Zax has no universal `Nothing` type and no source-level `null` value admitted
into unrelated types. `Nothing` names the per-type instance concept described
here, not a type a programmer declares or passes.

The same `?` and `!` forms can ask different domains their presence question.
Shared spelling does not merge identity, storage, lifetime, or behavior.

## A Nothing instance belongs to one concrete type

A type's Nothing instance supplies a stable semantic target when source has no
ordinary instance:

```zax
MyCounter :: type {
  count : Integer

  read final once : (result : Integer)() = {
    return _.count
  }
}

value := MyCounter.read() // 0 from the compiler-prepared Integer member
```

The compiler did not invoke an ordinary `MyCounter` constructor. It prepared
the Nothing representation needed by the reachable program as type-owned global
state. Exact global ordering remains deferred. The enclosing
Nothing instance is therefore not an ordinary completely constructed
`MyCounter`, and its preparation does not promise that:

- an ordinary zero-input constructor exists or ran;
- cross-member invariants of an ordinary `MyCounter` hold;
- the ordinary destructor will run; or
- programmer source can obtain the complete instance directly.

This distinction permits several compatible compiler-provided Nothing
instances to share backing storage. The language promises type-directed
behavior, not one address or one simultaneous set of ordinary resident
lifetimes.

An implementation may use shared or dedicated sentinels, tags, protected
storage, synthesized values, or another representation. Readable default
behavior cannot assume address zero will trap. Explicit trapping policy and
write protection may instead depend on target capabilities as described below.

### Backing exists only when reachable behavior needs it

Declaring a type does not by itself require addressable Nothing backing.
Neither does forming a concrete generic specialization that is never otherwise
used.

Backing becomes relevant only when reachable behavior can use that concrete
type's Nothing state—for example, a vacant pointer may be dereferenced or a
receiverless body may access `_`. Even then, the compiler may eliminate,
combine, or synthesize the backing when static analysis proves the observable
behavior is preserved.

An implementation may use weak or aggressive elimination. The language
requires the documented result when access occurs; it does not require an
unused global object merely because the type can theoretically have one.

## What compiler preparation provides

Compiler-provided Nothing recursively prepares common member forms:

```zax
MyCount :: identity admit expose type U32

MyState :: type {
  count : Integer
  identityCount : MyCount
  next : MyState *
  choice : MyValue?
  callback : ()()
}
```

For `MyState`'s compiler-provided Nothing instance:

- `count` reads as the integer default `0`;
- `identityCount` reads as `MyCount` containing its underlying `U32` default;
- `next` is a vacant `MyState *`;
- `choice` is an absent optional;
- `?callback` is false; and
- calling `callback` panics through its signature-compatible unavailable
  implementation.

Preparation is type-directed rather than byte-zeroing. A pointer's vacant
representation need not be zero, and a function's unavailable representation
may identify generated panic behavior.

`MyCount` is a representation-trivial identity: its complete stored
representation is the underlying `U32`. Nothing preparation can therefore use
the ordinary `U32` default without invoking identity admission syntax. An
identity with additional stored members follows the same recursive rules as
`MyState`.

A member form without a valid prepared representation uses its Nothing-access
panic or hardware trap when the operation and target can provide one. Arrays
currently have no general readable prepared Nothing form because their elements
require ordinary lifetimes:

```zax
MyArrayOwner :: type {
  values : Integer[5]

  first final once : (result : Integer)() = {
    return _.values[0] // invalid Nothing access; trap/panic when supported
  }
}
```

Future array work may define narrower preparation without making zeroed backing
into live elements. On targets unable to intercept the access, the missing trap
has the undefined boundary described below.

### Trivial types still have Nothing instances

Nothing behavior is not limited to aggregates:

```zax
readMe final : (result : U8)(pointer : U8 *) = {
  return pointer.
}

writeMe final : ()(pointer : U8 *, value : U8) = {
  pointer. = value
}

myU8 : U8 *

value := readMe(myU8)
// Built-in Nothing read: trap and panic when supported; otherwise undefined.

writeMe(myU8, 5)
// Nothing write: trap and panic when supported; otherwise invalid/undefined.
```

The vacant `U8 *` semantically targets `U8`'s Nothing instance. Reading its
direct built-in Nothing target uses the built-in trapping policy. Writing
compiler-provided Nothing backing is invalid even when the pointee type is
trivial.

This does not change prepared members inside a defined type's readable Nothing
instance. For example, `MyState.count` above still reads as `0`: that is a
prepared member of `MyState`'s Nothing backing, not a direct dereference of an
`Integer *` Nothing target.

## Select the type's Nothing policy

Omission uses the effective compiler-provided default. A type can state its
policy explicitly with the `once` constructor family.

### Compiler-prepared default

```zax
MyReadableType :: type {
  value : Integer

  +++ final once : ()() = default
}
```

`= default` explicitly requests the compiler-prepared behavior just described.

### Trapping Nothing

```zax
MyTrappingType :: type {
  value : Integer

  +++ final once : ()() = trap

  hasReceiver final once : (result : Boolean)() = {
    return ?_
  }

  read final once : (result : Integer)() = {
    // receiverless access traps/panics when supported; otherwise undefined
    return _.value
  }
}
```

`= trap` preserves a Nothing state:

```zax
present := MyTrappingType.hasReceiver() // false
```

It requests trapping backing rather than intentionally readable backing.
Member-independent receiverless code and `?_` remain usable.

The actual trap depends on target hardware. On a target with suitable read
protection, dereference, reference formation, or member access traps and enters
the applicable panic. Zax does not insert an “is this Nothing?” branch before
every potential read or write merely to emulate missing hardware support.

On a target without the required trap capability, an access that relies on that
trap has undefined behavior. The compiler may still use ordinary prepared
backing, generated panic functions, or debugger assistance, but none guarantees
that execution stops at the invalid access. Tooling must expose the selected
target's trap capability rather than presenting `= trap` as a portable software
check.

### Custom Nothing

```zax
MyMetrics :: type {
  receiverlessCalls : Integer

  +++ final once : ()() = {
    _.receiverlessCalls = 0
  }

  recordReceiverlessCall final once : ()() = {
    if !_
      ++_.receiverlessCalls
  }
}

MyMetrics.recordReceiverlessCall()
```

Combining the constructor form `+++` with `once` is how a type defines a custom
Nothing instance. `+++` prepares the type-owned state; `once` identifies this as
the receiverless constructor whose `_` is the Nothing instance rather than an
ordinary instance constructor.

The body requests dedicated type-owned storage and prepares one custom Nothing
instance. It:

- takes no constructor arguments;
- is not directly callable;
- does not create an ordinary zero-input constructor;
- may initialize writable state; and
- must complete before a receiverless route can publish the instance.

A custom Nothing instance does not require first-use or on-demand construction.
It will commonly be prepared during ordinary global startup. An implementation may
delay, reorder, or eliminate preparation only when doing so preserves the
eventual global/`once` lifecycle contract and proves no observable access can
occur first.

A custom Nothing instance is not intrinsically thread-safe. Concurrent mutation
needs the same explicit synchronization and affinity reasoning as other shared
state.

There is no direct `MyMetrics._`-like expression. The intended valid access is
through receiverless type calls. If unchecked pointer misuse reaches the same
backing, custom behavior may contain that failure, but it is not a public
Null-Object-style pointer API.

Exact global dependency order, startup publication, any permitted lazy
concurrent first use, failure/retry, module-instance identity,
generic-specialization identity, and teardown remain future global and `once`
lifecycle work.

## Set a scoped default

A module or namespace opening can choose the omission default for types declared
there:

```zax
[<nothing-instance-default=trap>]
namespace Services {
  First :: type {
    // Inherits trap.
  }

  [<nothing-instance-default=default>]
  namespace Testing {
    Fake :: type {
      // Inherits compiler-prepared default.
    }
  }

  Second :: type {
    // Inherits trap again.
  }
}
```

Resolution uses:

1. an explicit `+++ final once` declaration on the type;
2. the nearest directive attached to a namespace opening;
3. the module default; and
4. the language default for defined types, compiler-prepared readable Nothing.

The namespace directive belongs to one physical opening, not to the namespace
identity. A later reopening inherits its own lexical environment:

```zax
[<nothing-instance-default=trap>]
namespace Services {
  TrappingType :: type {
  }
}

[<nothing-instance-default=default>]
namespace Services {
  ReadableType :: type {
  }
}
```

Both declarations contribute to `Services`, but each type receives the default
visible in its opening. Nested openings inherit and restore lexically.
Conflicting values attached to one opening are errors; compatible repetition
is redundant. A module has one module-level default, so conflicting module
defaults are errors.

This scope-bound attachment avoids a stateful “from here onward” setting whose
meaning could change when files are reordered.

### Built-in types always use trapping Nothing

Language-provided built-in types share one canonical identity and one Nothing
policy across modules. Their own Nothing instances always request trapping
reads:

```zax
first : U8 *
second : U8 *

same := first == second // true, including when values cross module boundaries
```

The scoped `nothing-instance-default` directive does not alter this policy.
Modules cannot disagree about whether `U8`'s Nothing instance is readable or
trapping, and two vacant pointers to the same built-in type compare equal
regardless of which module produced them.

On suitable hardware, reading through a vacant built-in pointer traps and enters
panic. When the selected CPU cannot provide the trap, the access is undefined;
Zax does not add a software vacancy check before every built-in access. Writes
likewise use hardware protection whenever available.

## Receiverless and instance calls

One `once bound` declaration supports two routes:

```zax
MyCounter :: type {
  count : Integer

  inspect final once : ()() = {
    if ?_
      print("ordinary instance")
    else
      print("Nothing instance")
  }
}

counter : MyCounter

counter.inspect()  // counter supplies the receiver
MyCounter.inspect() // receiverless type call
```

For the receiverless call:

- the type identity performs lookup;
- no receiver source expression is evaluated;
- `_` identifies the type's Nothing instance; and
- `?_` is false.

For the instance call, the receiver expression evaluates once, `_` identifies
that instance, and `?_` is true.

`?_` is a protected receiver-presence operation inside a `once` function:

```zax
ordinary final : ()() = {
  if ?_ { // error: receiver presence is certain in a non-once bound function
  }
}
```

Postfix access binds first, so `?_.` instead dereferences `_` and applies the
ordinary selected `?T` operation to the resulting value.

`_` is pointer-shaped. `_.` supplies the dereferenced form. When an input
expects `T *`, `_` is preferred; when an input expects `T`, mapping may use the
automatic `_.` upgrade. Either form may be passed or returned, including a
verified `self` result.

```zax
MyMappedValue :: type {
  use private final : ()(
    input : MyMappedValue *
  ) unbound = {
    print("pointer overload")
  }

  use private final : ()(
    input : MyMappedValue &
  ) unbound = {
    print("reference overload")
  }

  takeValue private final : ()(
    input : MyMappedValue
  ) unbound = {
  }

  demonstrateMapping final once : ()() = {
    MyMappedValue.use(_)
    // Both overloads are viable, but exact MyMappedValue * is preferred.

    if ?_
      MyMappedValue.takeValue(_) // expected MyMappedValue automatically uses _.
  }
}
```

The overloaded `use(_)` call demonstrates preference: `_` can provide a
reference through automatic dereference, but the exact pointer parameter wins.
The programmer can always write `takeValue(_.)` when the dereference should be
explicit. Automatic promotion does not add presence proof; the example guards
the value path because it intends an ordinary instance.

An `unbound` function is different. It has no receiver slot at all, so `_` is
unavailable rather than present or absent.

### Receiver stance is statically known

A receiverless type call synthesizes a `copy`-stanced Nothing receiver before
callable selection:

```zax
MyResource :: type {
  inspect final once : ()() = {
  }

  consume final once : ()() last = {
  }
}

resource : MyResource

MyResource.inspect()
(resource as last).consume()
MyResource.consume() // error: receiverless copy cannot select exact last
```

No runtime rule changes `last` into `copy`. `MyResource.consume()` has no source
receiver declaration and therefore offers `copy`; the instance-qualified call
uses its actual receiver stance. An unstanced receiver declaration remains the
ordinary fallback for the receiverless `copy` offer.

Aliases, forwarding, and bound callable values preserve whether they represent
receiverless lookup or an installed instance receiver. They cannot erase that
distinction and guess at invocation time.

## Function values have presence

A function value declared without another initializer starts unavailable:

```zax
callback : ()()

if ?callback
  callback()
```

Recognized function presence returns exactly `Boolean`. A known fixed `final`
function is statically present, so its `?` result is `true` and its `!` result
is `false`. A varying function slot may require a runtime presence observation.

Calling a value proved unavailable is an error. Otherwise an unavailable call
panics through signature-compatible behavior. It does not execute a no-op,
invent results, rely on an invalid instruction address, throw an exception, or
unwind.

A `bound weak` callable with no ordinary or exceptional results is a separate
conditional-call contract. Any `except` result is a result and makes weak
storage unavailable. Invocation performs no body work when a valid resultless
weak slot is unavailable or when temporary strong promotion fails. Caller-side
argument/default evaluation and setup still occur. An assigned weak relationship
remains present after ownership closes, while an unavailable slot is absent;
neither condition makes invocation panic under this callable mode. Complete weak
callable behavior belongs to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#weak-invocation-is-conditionally-empty).

Reset releases any callable representation and captures, then restores the
unavailable state:

```zax
callback varying : ()() = makeCapturingCallback()

reset callback

if !callback
  reportNoCallback()
```

Typed default assignment is the explicit long form:

```zax
callback = (: ()())
```

`vacate` does not apply to function values because discarding owned capture
state without disposition would leak it. A final or readonly function slot
cannot be reset through an ineligible path.

Future function-type work may define an explicit no-op or default-result policy.
It is not the ordinary unavailable state.

## Pointer presence and vacancy

Vacancy describes the pointer's stored state. The pointer variable is
initialized and can be tested, assigned, reset, or vacated, but it does not
identify an ordinary target:

```zax
first : MyValue *
second : MyValue *

same := first == second // true: same exact pointer type, both vacant
```

`?pointer` asks whether the immediate pointer relationship is non-vacant. It
does not ask whether the represented address bits are nonzero or whether a weak
target remains live.

For a raw pointer, success proves only that the pointer is not vacant:

```zax
raw : MyValue *

if ?raw
  inspectAddress(raw)
```

It does not by itself prove that the address has valid lifetime, provenance,
alignment, ownership, or access permission.

For an owning pointer, presence reports ownership:

```zax
owner : MyValue * unique

if ?owner
  use(owner.)
```

A vacant `unique` or `strong` pointer owns nothing. It does not reference-count,
retain, release, or destroy the Nothing instance.

A weak pointer remains present while it stores a weak relationship, including
after strong ownership closes. `liveness probe observer` performs the separate
momentary observation of whether ownership is open; it acquires nothing and may
become stale immediately. Actual weak-to-strong construction both checks and,
on success, pins the allocation.

Some operations report failure by returning a vacant pointer instead of
panicking. `@!` does this when storage cannot be obtained:

```zax
owner : MyValue * unique = @!

if !owner
  reportAllocationFailure()
```

A weak-to-strong acquisition likewise leaves the new strong pointer vacant when
ownership has already closed:

```zax
observer : MyValue * weak
acquired : MyValue * strong = observer

if !acquired
  reportOwnershipClosed()
```

In each case the destination pointer keeps its declared role. A successful
`unique` result owns uniquely; a failed one is a vacant `unique` pointer that
owns nothing.

`OpaqueOwner`, `OpaqueObserver`, and `OpaqueReferenceObserver` preserve the same
layering while erasing target identity. Their complete behavior belongs to
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

Vacancy is not optional absence. `MyValue * unique ?` can be an absent optional,
a present vacant owner, or a present owner.

When a language-recognized pointer conversion receives a vacant source, it
produces the destination pointer type's own vacant state. The source and
destination need not use the same sentinel bits.

`unsafe cast` follows the same vacancy rule even though it checks nothing else:

```zax
barPointer : Bar *
fooPointer : Foo * = barPointer unsafe cast Foo *
// ?fooPointer is false: still vacant
```

A non-vacant address is reinterpreted unchanged. Code that needs a vacant
pointer's exact raw representation converts through a pointer-representation
integer, which does not preserve vacancy. See
[Zax conversions and casts](casting.md#vacant-pointers-stay-vacant).

Pointer arithmetic that requires an ordinary target may perform its own
category-specific check and panic. Disabling that check promises the pointer is
valid; arithmetic from Nothing then has undefined consequences.

## Reset and vacate are different

`reset` applies the relationship for which the pointer or its declaration has
authority, then leaves vacancy:

```zax
owner : MyValue * unique = @
reset owner
```

It may release unique or shared ownership, a weak observation, an allocation
schedule, or proved raw disposition authority. It is unavailable for an
ordinary borrowed raw pointer that has no disposition authority.

`vacate` only discards one raw address:

```zax
value : MyValue
borrowed : MyValue * = value

vacate borrowed
```

It installs vacancy without disposing the target.

Plain `vacate` requires proof that:

- the pointer is raw and non-owning;
- no declaration-attached disposition responsibility is being bypassed; and
- discarding this address loses no required disposition authority.

An opaque but potentially valid external relationship can accept explicit
responsibility:

```zax
opaqueRaw : MyValue * = obtainOpaqueBorrow()
unsafe vacate opaqueRaw
```

This form is available only for raw pointers. A proved last usable address to a
live open-ended allocation, or a scheduled raw pointer still carrying the live
schedule, remains an error even under `unsafe`.

Self-managing pointer roles always reject vacate:

```zax
anotherOwner : MyValue * unique = @
unsafe vacate anotherOwner // error: ownership must be released or transferred
```

The same applies to strong, weak, interior, and other managed pointers. Unsafe
source may assert an opaque but potentially valid raw relationship; it cannot
legalize a guaranteed leak or corrupt ownership accounting.

## Pointer dereference is unchecked

Postfix pointer dereference does not ordinarily test vacancy:

```zax
pointer : MyValue * = choosePointer()
view : MyValue & = pointer.
```

The reference binds to the selected target place. If `pointer` is vacant at
runtime, it mechanically reaches `MyValue`'s Nothing backing. A reference has
no separate vacancy state and never rebinds.

When vacancy is statically certain, the compiler reports the misuse:

```zax
vacant : MyValue *
view : MyValue & = vacant. // error: pointer is proved vacant
```

Analysis is intentionally incomplete. A compiler may diagnose more certainly
invalid uses as its analysis improves; source is not limited to mistakes every
compiler can prove.

After an unchecked runtime vacancy:

- reading a prepared member returns its prepared value;
- accessing an unprepared member panics;
- a trapping policy panics at dereference, reference formation, or member
  access;
- accidental access to a custom Nothing instance may execute its custom
  containment behavior; and
- writing compiler-provided shared backing is invalid.

This is the narrow exception to the ordinary teaching that a reference cannot
target Nothing. The reference representation itself is not nullable, and the
reference still has one fixed backing place; unchecked pointer dereference may
bind that place to special Nothing backing.

## Hardware-assisted traps are not portable guarantees

When target hardware can protect compiler-provided Nothing backing from writes,
the compiler uses that protection for every policy. An attempted write then
traps and enters panic. There is no separate `write trap` policy: write
protection is always desirable when the target can provide it cheaply.

Unavailable function members similarly use generated panic implementations and
may use hardware trap mechanisms where the target supports them.

Lower-end or unusual processors may provide no suitable read or write trap.
Zax does not compensate by inserting a vacancy branch before every potential
load, store, reference formation, or trivial pointer access. A debugger may
notice changed sentinel data or another symptom after the operation, but that
is not equivalent to trapping at the faulting instruction.

On such a target, behavior that depends on a missing hardware trap is undefined.
Readable members may still return their prepared defaults, and generated panic
functions still panic when the call representation can route to them, but the
compiler cannot promise universal interception.

## Invalid writes are not checked on every store

Writing compiler-provided Nothing backing is a contract violation:

```zax
pointer : MyCounter * = chooseCounter()
pointer.count = 1
```

When analysis proves `pointer` vacant, the compiler reports an error. When it
cannot prove the runtime state, Zax does not require a generated vacancy check
on every store. Such checks would impose unacceptable cost on ordinary writes.

Suitable target memory protection catches the write and enters panic. Without
that hardware support, debug instrumentation may discover some violations, but
a missed write can corrupt shared Nothing backing or have undefined
consequences. The programmer remains responsible for establishing ordinary
target validity.

The trapping policy deliberately chooses another tradeoff and requires a panic
for dereference or access even when the target lacks a cheap hardware trap.

A custom Nothing constructor may write its dedicated storage, and intended
receiverless type calls may later mutate it through compatible declared
qualifications. Accidental vacant-pointer access remains misuse even when the
custom implementation contains the failure.

### Possible write flow needs a future unsafe boundary

A proved write to compiler-provided Nothing is an error. Merely suspecting that
a pointer may become vacant before a later write is different: writing through
a pointer is not inherently unsafe, and the responsible source location may be
the earlier call or handoff that exposes the pointer to a writer.

That uncertainty is a safety concern, not intent acknowledgement. Future
safety/error work retains provisional `possible-nothing-write` and
`possible-nothing-read-trap` catalog entries. It must define their exact trigger
scenarios, placement, and whether stronger compiler-specific findings use
standard behavior or explicitly namespaced extension diagnostics. Current
source does not acquire a broad vanilla `unsafe` escape for every pointer write.

The read category applies only when a possibly vacant pointer could reach a
built-in type's canonical trapping Nothing or a defined type selecting
`= trap`. Neither category disables an actual hardware trap or legalizes a
proved vacant access.

## Optional absence remains independent

Optional absence requires no Nothing instance:

```zax
empty : MyValue?
presentVacant : MyValue * ? = (: MyValue *)
```

`empty` has no boxed lifetime. `presentVacant` has a live boxed pointer whose
value is vacant. The same distinction applies to an optional function value:
outer absence, a present unavailable function, and a present callable are
different states.

Nested optionals likewise retain each wrapper layer. Representation may use
niches when every semantic state remains available, but physical reuse does not
merge the states.

## Costs and diagnostics

Programmers and tools need to expose:

- the effective compiler-default, trapping, or custom policy;
- whether that policy was explicit, inherited from a namespace opening,
  supplied by the module, or defaulted by the language;
- the fixed trapping policy and canonical identity of built-in types;
- whether a member path has a prepared value, generated panic behavior, or a
  target-dependent trap;
- receiverless versus instance-qualified call mode;
- the synthesized receiverless `copy` stance;
- static proof, unchecked access, required panic, or optional debug detection;
- custom global initialization and synchronization;
- capture disposition during function reset;
- pointer release through `reset` versus address discard through `vacate`;
- safe vacancy remapping versus raw representation-preserving cast; and
- target support for read/write traps and the undefined boundary when the
  required hardware capability is absent;
- debugger-only detection that is not a trap guarantee; and
- target costs for tags, protected storage, or dedicated backing.

Representative diagnostics include:

- use of legacy `Nothing` as a type;
- type-qualified call to a non-`once` bound function;
- `?_` in a non-`once` function;
- receiverless call to an exact non-`copy` receiver;
- proved vacant pointer dereference or write;
- unprepared or trapping Nothing access;
- conflicting Nothing-policy directives on one namespace opening or module;
- selected target cannot provide a requested trap capability;
- incomplete custom Nothing preparation;
- `vacate` on a managed or live scheduled pointer;
- `unsafe vacate` that would guarantee a leak;
- reset through an ineligible function slot; and
- raw cast whose represented Nothing is not valid for the destination.

Exact diagnostic identifiers, panic-category controls, ABI, and reflection APIs
remain future work.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, ABI,
implementation mapping, or conformance specification.

Later work may refine representation and analysis while preserving:

- no universal `Nothing` type or null value;
- one semantic Nothing policy per concrete type;
- one canonical trapping Nothing policy for each built-in type across modules;
- compiler-prepared, hardware-capability trapping, and custom policy choices;
- namespace-opening and module defaults that explicit type declarations
  override;
- receiverless `once` calls using a `copy`-stanced Nothing receiver;
- pointer vacancy remaining distinct from optional absence and ownership;
- unavailable function presence and lifecycle-aware reset;
- unchecked pointer dereference with diagnostics for proved misuse;
- no mandatory software check on every compiler-Nothing read or write;
- raw-only `vacate` and rejection of guaranteed leaks;
- custom Nothing remaining type-call-oriented rather than a direct pointer API;
- representation freedom without CPU-null dependence; and
- visible costs, failures, and unsafe responsibility.
