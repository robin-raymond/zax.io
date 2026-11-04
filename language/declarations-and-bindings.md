# Zax declarations and bindings

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing declaration, binding, initialization, name-resolution, and assignment boundaries; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Value declaration forms, including anonymous declarations, explicit discard names, and unread-name acknowledgement; default, direct, and inferred initialization; binding visibility; declaration and result transfer stance; ordinary versus exceptional result-marker placement; declaration-facing compatibility-posture attachment, strict defaulting, and explicit retention; redeclaration and shadow permission; `outward` declarations; one lexical identifier namespace; qualified-path resolution through incomplete declarations; explicit instance-member lookup; composition-facing member, route, role, and fulfillment declaration forms; bound/unbound function prototypes, fixed function implementation storage, and type-callable `once` declaration integration; declaration-facing qualifier axes and attachment, including declaration-side replacement permission; exact type/union/variant/variable/namespace/reshape alias declaration integration; operator-phrase and literal-operator declaration ownership, type-parameter slots, uncommitted generic result slots, and type-qualified operator discovery; bounded private member eligibility; the declaration-versus-assignment boundary; the general non-value definition family, no-storage `reshape`, and identity-declaration integration; named type self-reference and general forward anchors; declaration diagnostics and formatting |
| Does Not Own | Complete ordinary type meaning ([type definitions](type-definitions.md)); unmanaged overlays ([unions](unions.md)); managed alternatives ([variants](variants.md)); complete namespace/module/import/export behavior ([namespaces and modules](namespaces-and-modules.md)); complete transfer meaning ([transfer stances](transfer-stances.md)); integer realization and numeric-source candidate behavior ([integer literals and realization](integer-literals.md)); complete literal payload, lookup, merge, join, and execution behavior ([literal source and operators](literal-source-and-operators.md)); complete [optional behavior](optional-values.md); function invocation/result routing ([function invocation](function-invocation.md)); cohesive [exceptional result flow](except.md); complete [composition behavior](composition.md); `using` resource enrollment and disposal ([Zax `using`](using.md)); source token/layout behavior ([source structure](source-structure.md)); qualifier semantics ([qualifiers](qualifiers.md)); transparent alias/identity semantics ([identity types](identity-types.md)); or enum members and policies ([enums](enums.md)) |

## Mental model

Zax makes name introduction visible.

```zax
x = 1
y := 1
```

`x = 1` uses an existing destination. It does not create `x`.

`y := 1` declares `y`, infers its type from `1`, and initializes it.

This distinction separates three concepts:

- A **declaration** introduces a name and determines what kind of entity it
  denotes.
- **Initialization** establishes the first state of a declared value.
- **Assignment** invokes an operator using an already declared destination.

An unresolved assignment target is therefore an error, not an implicit local
declaration.

Declaring a place is what constructs it. The `=` in `item : Item = source`
belongs to the declaration: it supplies the source for that construction.
Some places are declared in one location and constructed in another. A
function result is declared by its prototype and starts empty; a member is
declared by its type and may be constructed inside a constructor body. Code
constructs into such an already declared place with `.=`:

```zax
item : Item = source
// Declaring `item` constructs it.

item = other
// `item` already exists and is live: ordinary assignment.

make final : (
  result : Item
)() = {
  result .= source
  // `result` was declared by the prototype and starts empty.
  // `.=` constructs a value into it.
}
```

On a place that is already live, `.=` instead replaces the value. A
declaration never needs `.=`, because introducing the place is the act of
construction. The uses of `.=` are introduced in
[Zax construction, replacement, and destruction](construction-and-destruction.md#construct-into-an-existing-place-with-dot-equals).

## Core value forms

| Form | Meaning |
| --- | --- |
| `name : Type` | Declare `name` with an explicit type and perform the type's default initialization. |
| `name : Type = value` | Declare `name` with an explicit type and initialize or construct it directly from `value`. |
| `name := value` | Declare `name`, infer its type from `value`, and initialize it. |
| `name = value` | Invoke an assignment operator using an already declared destination. |
| `name .= value` | Construct a new value into an already declared place: first construction when the place is empty, replacement when it is live. |

### Inferred declarations

`:=` is the adjacency of declaration and initialization:

```zax
x := 0
y : = 0
```

Both forms declare an inferred `Integer` and initialize it from `0`. A formatter
uses the compact `:=` spelling consistently.

A number such as `0` does not choose an integer width by itself. Because these
declarations provide no explicit type, Zax uses the ordinary default
`Integer`. Writing `x := +0` instead selects the unsigned default `UInteger`.
The complete rules are in
[Zax integer literals and realization](integer-literals.md).

Compatibility posture does not propagate through ordinary inference. A
producer result may offer contextual compatibility at its immediate mapping
boundary, but:

```zax
captured := makeCompatibleResult()
// captured has the inferred concrete identity and compatible strict.
```

An explicit partial type declaration may retain posture while inferring the
base type:

```zax
captured : compatible shape = makeCompatibleResult()
```

Complete posture behavior is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#posture-does-not-propagate-accidentally).

The compiler may optimize storage reservation and initialization when doing so
preserves behavior. That does not collapse the programmer-visible distinction
between introducing a name and initializing its value.

### Array declarations and expressions

Array declarations preserve the same declaration-versus-assignment boundary:

```zax
fixed : Integer[3] = [ 1, 2, 3 ]
bounded : Integer[2 in 0..10] = [ 4, 5 ]

inferred := [ 6, 7, 8 ]
// Integer[3] using the resolved default storage profile

fixed = [ 9, 10, 11 ]
// Assignment to an existing array; no declaration occurs.
```

In `Integer[2 in 0..10]`, `2` is the number of elements initially constructed.
The `0..10` part describes the sizes the array may have later. Neither number
states how much backing storage is reserved.

When the declaration does not provide an element type, all array entries must
agree on one. Uncommitted number literals can take the type established by
equal committed entries. Two different committed element types are an error;
the first entry never wins merely because it appears first.

An empty array or a list containing only constructor packets cannot determine
its own element type and therefore needs a typed destination.

Expanding a runtime slice or iterable creates an open resizable array using the
resolved default storage profile. A bare array or slice remains one element;
array-entry `from` explicitly contributes its sequence values. Complete
examples are in
[Zax arrays and slices](arrays-and-slices.md#array-expressions).

### Anonymous declarations and discard names

An ordinary declaration may omit an accessible name:

```zax
{
  : Resource = acquire()
  work()
}

{
  # : Resource = acquire()
  work()
}
```

These are alternative spellings with the same value behavior. Each constructs
one anonymous `Resource`, introduces no identifier that later source can use,
and destroys the value when its surrounding block ends. Outside a mapping
construct such as function result routing or `using`, the missing name and
explicit `#` have no different construction, elision, lifetime, or destruction
effect. `#` only makes the programmer's discard intent explicit.

Their inferred counterparts are also legal:

```zax
: = makeFirst()
# := makeSecond()
```

The surrounding context still must accept a declaration at that position. In a
result-routing group or `using` list, bare `:` and `#` additionally control
destination or disposal behavior and are therefore not interchangeable. Those
contextual differences are defined by
[Zax function invocation](function-invocation.md#source-and-destination-discard)
and [Zax `using`](using.md#anonymous-enrollment-and-discard).

A declaration must provide either an explicit type or an initializer from which
the type can be inferred:

```zax
i: // error: no explicit or inferable type
```

Inference uses the initializer and its immediate context. It does not scan later
statements to discover intended use:

```zax
value := makeValue()
```

Some type shape may be explicit while the remaining type is inferred:

```zax
reference : & = original
pointer : * = original
```

The complete inference algorithm and exact set of partially explicit qualifiers
remain later design.

`#` in the name position, as in `# : Resource`, introduces no name. `#` between
a name and its type keeps the name and is the unread-binding marker described
next.

### Names the body may leave unread

A binding can exist because it is constructed, required by a signature, or
stored for its lifetime, while this scope never reads the name. Put `#` between
the name and the type:

```zax
{
  lock # : MyLock = mutex
  work()
}
```

`lock` is an ordinary binding. The marker says this scope may finish without
reading it. `MyLock` is still constructed at entry and destroyed at block exit.

The same marker on a parameter does not remove the argument from the call. The
body is excused from reading the name; the caller still supplies it. The marker
is not part of the callable contract, so another implementation of the same
prototype may read the parameter:

```zax
ignore final : ()(
  input # : Integer
) = {
}
```

Leave the marker off, and an unread named binding is an intent error. The name
looks forgotten:

```zax
{
  lock : MyLock = mutex // error: lock is never used
  work()
}
```

This is not an `intent<...>` acknowledgement. An intent enclosure confirms a
suspicious operation that is present in the source. An unread binding has no
such operation. The `#` marker is the acknowledgement, and it belongs on the
declaration.

When the name is unnecessary, use the anonymous form from the previous section
instead of marking the type:

```zax
{
  : MyLock = mutex
  work()
}
```

A result uses the same marker position for a different permission. In
`diagnostic # : String`, `#` lets the caller omit that result. The function
still produces it. Caller acknowledgement is taught by
[function invocation](function-invocation.md#required-and-discardable-results).

A name this body did not declare is acknowledged where it is already in scope:

```zax
value #
```

`value #` is a complete statement. It does not declare `value`. A declaration
continues with `:` or `:=`. The statement is legal only for a binding that
already exists. An inherited lambda parameter is the ordinary case;
[lambdas](lambdas-and-callable-composition.md#inherit-a-complete-prototype)
show it on a complete callback.

Attached `#` is a different operation. It reads the value:

```zax
myBits : U8 = 16
count := #myBits
```

`#myBits` is population count. The
[integer operator catalog](integer-operator-catalog.md#counts-and-reductions)
owns that family. A spaced prefix such as `# value` is not an unread-name
acknowledgement.

### Direct initialization

An initialized typed declaration performs direct initialization:

```zax
item : Item = source
```

It does not default-initialize `item` and then invoke later assignment. That
alternative could introduce an extra constructor, destructor, allocation, or
other visible operation.

Initializer and constructor selection are defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md).
The declaration guarantees only that `item` is introduced and initialized
directly from the supplied source.

Dynamic allocation uses the same direct-initialization boundary:

```zax
owner : Item * unique = @
scoped : Item * = @
```

`@` obtains storage, constructs `Item`, and initializes the pointer declaration.
It does not first create a vacant pointer and assign over it. A pointer
declaration without an allocation initializer is vacant:

```zax
emptyOwner : Item * unique
emptyView : Item *
```

An unbound assignment target still cannot supply a destination:

```zax
existing = @ // error
```

A previously declared typed pointer does supply one:

```zax
existing : Item *
existing = @
// Legal open-ended raw allocation; existing must be reset manually.
```

The explicit long form is also legal:

```zax
existing =
  (: Item * = @) as last
```

For raw `@`/`@!` destinations, the declaration's life path schedules allocation
disposition. `@<`/`@!<` deliberately leaves a successful raw allocation
open-ended. Complete source, policy, pointer roles, and cleanup behavior are
defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocate-through-a-declaration).

Direct allocation assignment uses the existing pointer destination's contract:

```zax
scheduled : Item * = @
reset scheduled
scheduled = @{ anotherArena }

ordinary : Item *
ordinary = @ // legal, but open-ended and not automatically reset
```

Input parameters are different. Their initializer syntax declares an omission
default rather than a cleanup obligation on the parameter slot:

```zax
useItem final : ()(
  item : Item * = @
) = {
  // item is an ordinary borrowed raw pointer.
}
```

When omitted, invocation machinery owns the scheduled allocation temporary
through call completion. Inside the body, `item = @` is ordinary raw allocation
assignment: it creates an open-ended allocation that the callee must manually
track because the borrowed parameter has no declaration-attached schedule.

An initializer may state its own transfer stance. If it does not, `copy` is
still present as the default. A destination declaration stance becomes active
only after construction:

```zax
makeItem final : (
  result : Item move
)() = {
  // ...
}

item : Item copy = makeItem()
```

`makeItem()` offers `move` while constructing `item`. Later ordinary uses of
`item` offer `copy`. Complete stance meaning and fallback are defined by
[Zax transfer stances](transfer-stances.md).

Optional declarations preserve the same distinction among same-type
construction, first-layer construction, and acknowledged additional depth:

```zax
sourceFooOptional : FooType?
fooOptional : FooType? = sourceFooOptional
// same resolved type: direct optional construction

bar : BarType
barOptional : BarType? = bar
barOptionalFromPacket : BarType? = [{ bar }]
// both construct the first optional layer

someOptional : SomeType?
someOptionalOptional : SomeType? ? = someOptional // error: packet required

someOptionalOptionalFromPacket : SomeType? ? = [{ someOptional }]

anotherOptionalOptional : SomeType? ?
someOtherOptionalOptional : SomeType? ? =
  anotherOptionalOptional
// same resolved type: direct nested-optional construction
```

Direct construction does not first create absence and assign over it. The packet
in the depth-changing case distinguishes deliberate outer wrapping from
same-type construction.

Complete optional construction and depth behavior is defined by
[Zax optional values](optional-values.md#adding-optional-depth-requires-a-packet).

A number literal can take its type directly from an explicitly typed
declaration:

```zax
myByte : U8 = 255
myTooLarge : U8 = 256 // error: 256 is outside U8
```

The compiler checks the value against `U8`; it does not first create an
`Integer` and convert it. See
[Zax integer literals and realization](integer-literals.md#direct-typed-realization).

## Default initialization

Every ordinary value declaration initializes its value:

```zax
count : Integer
item : Item
```

For an optional type, default initialization constructs an absent wrapper and
does not run the boxed type's zero-input constructor:

```zax
empty : Item?
present : Item? = [{}]
```

The second declaration explicitly uses the canonical zero-entry construction
packet and therefore constructs a present optional containing one
zero-input-constructed `Item`. For a non-optional `Item`, `item : Item` and
`item : Item = [{}]` select the same zero-input construction when available.
See [Zax optional values](optional-values.md#empty-construction-packets).

Default initialization may initialize contained values, execute constructors,
allocate storage, or perform other visible work. Constructors, replacement
constructors, and destructors return no results, and Zax does not use
exceptions. Initialization may nevertheless encounter a panic such as
allocation failure or stack exhaustion.

A declaration followed by assignment performs two operations:

```zax
x : Integer; x = 1
```

This default-initializes `x` and then invokes assignment. It is equivalent in
those respects to:

```zax
x : Integer
x = 1
```

It is not an initialize-later form. A local declaration always constructs its
value when it is declared; Zax has no form that leaves a local empty for later
construction.

### Low-level initialization belongs in a constructor

Sometimes storage should be filled by an operation the compiler cannot see,
such as assembly or a device read, without first paying for ordinary
initialization. Put that storage in a type and establish it in the type's
constructor:

```zax
DeviceBuffer :: type {
  bytes : U8[4096]

  +++ final : ()(device : Device &) = {
    unsafe<opaque-construction>{ device.readInto(_.bytes) }
  }
}

buffer : DeviceBuffer = [{ device }]
```

`buffer` is an ordinary local that is constructed when declared. Inside the
constructor, the unsafe enclosure tells the compiler that `readInto` constructs
`bytes`, so `bytes` receives no automatic initialization first. The wrapper
type costs nothing at run time, and the unsafe responsibility sits on the one
operation that needs it. Complete member behavior is defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md#construction-by-an-opaque-operation).

## Scope entry and redeclaration

An ordinary value binding enters scope only after initialization completes. The
new name does not participate in lookup while its type and initializer are being
evaluated:

```zax
x := use(x) // error if no outer x exists
```

If an outer `x` exists and permits shadowing, the right-hand `x` resolves to that
outer binding. The new inner `x` becomes visible afterward.

Same-scope redeclaration is always an error:

```zax
x : Integer
x := 1 // error: x is already declared in this scope
```

Semicolon composition does not introduce another scope:

```zax
x := 1; x := 2 // error: same-scope redeclaration
```

Earlier operands may introduce names for later operands:

```zax
x := makeValue(); y := use(x)
```

The source formation, body, and clause-scope rules that make this possible are
defined by [Zax source structure](source-structure.md).

### Deferred function bodies

A function body is delayed code rather than an expression executed while the
function value is initialized. It may refer to its own completed binding:

```zax
factorial final : (result : Integer)(n : Integer) = {
  return n <= 1 ?? 1 ;; n * factorial(n - 1)
}
```

This is a recursive binding for a delayed body, not permission for an ordinary
initializer to self-reference. Ordinary surrounding captures are discovered
lexically and materialized when the function value is constructed. A function's
self-reference cannot be an ordinary by-value copy of a function value that does
not yet exist.

A bound lambda instead uses its generated receiver for direct self-recursion:
`_(...)` calls that receiver. Complete
capture and recursion behavior is defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#self-recursion-uses-the-lambda-receiver).
Mutually recursive `forward` requirements remain later callable work.

### Default function values

A function value declared without another initializer still receives default
initialization:

```zax
callback : ()()
```

Its default value is the function type's unavailable state. Recognized
`?callback` presence is false. The compiler diagnoses an invocation it can prove
still targets that state; an otherwise unhandled invocation panics rather than
manufacturing arbitrary results or executing undefined code.

Call-boundary behavior for that state is defined by
[Zax function invocation](function-invocation.md#callable-prototypes-and-visible-contracts).
The relationship to per-type Nothing instances, function reset, and
signature-compatible panic behavior is defined by
[Zax Nothing instances](nothing-instances.md#function-values-have-presence).

Future explicit behavior may permit selected function types to request a no-op
or default-result unavailable implementation. That is a distinct future
function-type contract.

## Shadowing

Nested shadowing is prohibited by default. The outer declaration that may be
hidden must grant permission:

```zax
value shadowable := 1

if condition {
  value := 2
}
```

`shadowable` belongs to the declaration that is willing to be hidden. It is not
a marker placed on the inner declaration after the conflict is found.

The newly introduced inner declaration is not automatically `shadowable`:

```zax
value shadowable := 1

if outer {
  value := 2

  if inner {
    value := 3 // error: the middle value is not shadowable
  }
}
```

Permission can be propagated deliberately:

```zax
value shadowable := 1

if outer {
  value shadowable := 2

  if inner {
    value := 3
  }
}
```

The rule applies to ordinary lexical declarations that can be shadowed,
including values, types, aliases, imports, and namespaces. A forward declaration
and its completion are one declaration rather than shadowing. Overload groups use
category-specific rules in their future owners, and flow labels are a separate
category described under
[flow labels and the ordinary namespace](#flow-labels-and-the-ordinary-namespace).

Once a name is shadowed, ordinary lookup no longer reaches it. Zax does not
provide a general parent-scope or `..` lookup operator. A programmer who needs
both entities can establish an alias before shadowing.

Namespace openings have one narrow local rule. In:

```zax
namespace Utilities shadowable {
  Utilities :: forward type
}
```

`shadowable` permits this opening's body to introduce a declaration that hides
the namespace's own unqualified name. It is not a property of the namespace
identity and does not grant permission to hide unrelated outer declarations.
The complete declaration/reopening rule is defined by
[Zax namespaces and modules](namespaces-and-modules.md#self-name-shadow-permission-belongs-to-one-opening).

## Outward declarations

A branch is a scope, so a declaration inside it normally ends with the branch.
Sometimes the branch exists only to decide *how* to initialize something that
later code needs:

```zax
if useLarge {
  limit final : Integer = 1000
} else {
  limit final : Integer = 10
}

process(limit) // error: each limit ended with its branch
```

Declaring `limit` before the `if` and assigning it in each branch would not
work, because `limit` is `final`. Marking the declaration `outward` makes it
belong to the scope around the construct instead:

```zax
if useLarge {
  scratch := measure()                  // stays inside the branch
  limit outward final : Integer = 1000
} else {
  limit outward final : Integer = 10
}

process(limit) // valid: limit was declared and initialized exactly once
```

`outward` sits on the declaration-name side, where `shadowable` sits. For a `::`
declaration it comes just before the `::`:

```zax
MyIndex outward :: alias type U64
```

Only the marked declarations escape. Helpers in the same branch stay local.

### Where an outward declaration lands

An outward declaration lands in the scope containing the nearest enclosing
`if`, `switch`, or explicit `scope`:

- a plain `{ ... }` block between the declaration and that construct is passed
  through;
- an `if`/`else if`/`else` chain is one construct, so a declaration in any of
  its branches lands outside the whole chain; and
- each construct is crossed one level at a time:

```zax
if outerCondition {
  if innerCondition {
    limit outward := 10   // lands in the outer branch, not beyond it
  } else {
    limit outward := 20
  }
  use(limit)              // valid here
}

use(limit)                // error: limit did not escape the outer if
```

To lift a declaration further, write the decision as one chain.

Inside a loop, a branch's outward declaration lands in the loop body, which is a
fresh scope on every pass, just like any other declaration there:

```zax
forever {
  if ready {
    item outward := take()
  } else {
    item outward := wait()
  }
  process(item)              // valid: this pass's item
}
```

What `outward` cannot do is escape a loop itself. A loop body runs repeatedly
and may not run at all, so a declaration written directly in a loop body has no
single place outside the loop to land:

```zax
forever {
  item outward := take()     // error: the nearest enclosing construct is the loop
}
```

`outward` with no enclosing `if`, `switch`, or `scope`, such as in a plain block
directly in a function body, is also an error.

### Every path declares it exactly once

After the construct, the name must have exactly one meaning, whichever way the
program went.

**Runtime branches.** Every path that reaches the code after the construct must
declare the same name with the same type and qualifiers. The result is one
declaration, initialized by whichever path ran. Different declarations on
different paths are an error at the construct. A path that leaves through
`return`, a `break` to an outer target, or a panic never reaches the following
code, so it needs no declaration:

```zax
if mode == 1 {
  limit outward final : Integer = 10
} else if mode == 2 {
  return                                // leaves: needs no limit
} else {
  limit outward final : Integer = 100
}
```

A runtime `if` therefore needs a final `else` whenever it declares outward.
Behavior inside `switch`, including fallthrough, is described by
[Zax switch, case, and default](switch.md#outward-declarations-in-cases).

**Compile-time selection.** In an `if compile` or `switch compile`, only the
selected branch exists, so branches may declare different things. A name that
the selected branch does not declare is simply an unknown name where it is used.
See [compile-time code](compile-time-code.md#selecting-declarations).

### Outward declarations and shadowing

An outward declaration behaves exactly as if it had been written in the scope
it lands in. If that scope already declares the same name, the result is a
duplicate declaration, not shadowing. Its relationship to declarations in
scopes further out follows the ordinary [shadowing](#shadowing) rules, and its
lifetime ends with the scope it lands in.

## Instance members

Instance members are not injected into an instance function's ordinary lexical
lookup. Access through the current instance uses `_` explicitly:

```zax
value := 0

value = 1
_.value = 2
_.foobar = 3
foobar = 4 // error: no local foobar is declared
```

The same principle applies to instance member functions. Static or shared
members use their appropriate qualified form.

Explicit access prevents copied code, a newly added member, or a forgotten local
declaration from silently changing name resolution.

### Composition-facing member declarations

A stored member may independently publish names, offer an expected-type route,
or expose behavior through composition:

```zax
Assembly :: type {
  publicData own : PublicData
  engine preferred : Engine
  controls expose : Controls
}
```

The three words answer separate questions:

| Word | Declaration effect |
| --- | --- |
| `own` | Publish eligible stored names from the member |
| `preferred` | Let an expected member type select this member from the containing value |
| `expose` | Generate mechanically safe forwarding behavior on the containing type |

Each word is legal without either of the others, and any subset may be combined:

```zax
Car :: type {
  engine own preferred expose : Engine
}
```

When several appear, their canonical relative order is
`own preferred expose`. A later general grammar and formatting sweep will place
this modifier group relative to unrelated declaration and qualifier words.

A route declaration names one particular target instead of publishing a whole
eligible surface:

```zax
value via primary.value                 // data route to one stored place
start final : ()() = via engine.start   // callable adapter to one operation
owner final : (result : Car & ?)() =    // checked outer-result mapping
  tracked via engine.selectedPeer
```

`tracked via` is not a general alternate routing mode. It is the explicit
callable form for a route whose result uses tracked immediate outer casting and
therefore remains optional.

Callable declaration forms distinguish one operation from a whole overload
family:

```zax
stop final : ()() = existing
reset final : ()() = existing family
restart final : ()() = via family engine.reset

operator binary '+' final :
  (result : Power)(rhs : Fuel) readonly =
    via family engine.operator binary '+'

blocked final : ()() = forbidden family
```

Singular `existing` or `via` selects exactly one operation. `existing family`
discovers one callable family, while `via family` names one and may give it a new
outer name. The written prototype is an exact anchor; every eligible family
member must independently obtain one unambiguous mechanical mapping. An
unmappable overload added later is an error rather than being silently omitted.
`forbidden family` fences the complete outer visible callable name, or the
complete structured operator form, regardless of which source would supply it.
A later direct declaration in that outer family conflicts with the fence.

An abstract role describes something that an immediate container must declare,
or may elect to declare when the role is optional. The container makes that
connection explicit with `fulfill`:

```zax
start abstract : ()()

begin fulfill contract.start final : ()() = {
}
```

`start abstract` is requirement metadata, not a bodyless callable and not
storage. `begin` is an ordinary callable declaration whose `fulfill` clause says
why it exists and identifies the exact role it satisfies.

A private stored contract can still activate roles through `own` without
publishing a public data path:

```zax
ServiceContract :: type {
  start abstract : ()()
}

Service :: type {
  contract own private : ServiceContract

  begin fulfill contract.start final : ()() = {
  }
}
```

The carrier remains private. The directly written fulfilling declaration chooses
its own visibility independently; explicit fulfillment does not reveal the
carrier or its stored data.

An optional role remains available for signature checking but does not make its
fulfillment mandatory:

```zax
TelemetryContract :: type {
  report abstract optional : ()(event : Event readonly &)
}

Service :: type {
  contract own private : TelemetryContract

  report fulfill contract.report final : (
    event : Event readonly &
  )() = {
  }
}
```

Omitting `report` from `Service` would also be valid. If it is present, the
`fulfill` path remains explicit and all normal compatibility checks apply.
`abstract optional` supplies no default body or runtime hook.

Ordinary omission in an abstract role resolves qualifier defaults just as it
does elsewhere. A callable role's omitted type-side stance is therefore
`varying`, and a `final` fulfilling declaration is compatible with it because
declaration-side `final` restricts only that declaration. A role that writes
`final` on its type side rejects a `varying` fulfillment. See
[composition](composition.md#final-fulfillment-of-a-callable-role).

`abstract relaxed` is the narrow opt-in that instead leaves only otherwise
defaulted outer value axes, or callable receiver axes, open:

```zax
ObserverContract :: type {
  observe abstract relaxed : ()()
}

MyObserver :: type {
  contract own private : ObserverContract

  observeReadonly fulfill contract.observe final : ()() readonly = {
  }

  observeWritable fulfill contract.observe final : ()() mutable writable = {
  }
}
```

Each qualification-specialized declaration independently names and satisfies
the relaxed role. They do not form a dispatch table or merge into one
implementation. Explicit qualifiers remain required, and relaxation does not
change parameter or result types, base type, arity, indirection, transfer stance,
labels, or provenance.

Optionality and relaxation are independent. `abstract optional relaxed` permits
zero or more distinct qualification-specialized fulfillments. Without
`optional`, a relaxed role still requires at least one fulfillment. The
canonical relative order is `abstract optional relaxed`.

Published member names remain instance-member lookup. They are not injected
into an instance function's lexical scope. `shadowable` remains a lexical-name
permission and does not affect composition publication or collision rules.

Because abstract metadata has no declaration place, declaration-side words do
not apply. `private` is also invalid: the immediate container must be able to
inspect the role. A qualifier that belongs to the required type remains valid:

```zax
label abstract final : String // error: declaration-side `final`
label abstract : String final // valid: `final` qualifies String
```

The complete publication, projection, routing, fulfillment, and collision rules
are defined by [Zax composition](composition.md).

### Bound and unbound function storage

`bound` means an implementation has a receiver available through `_`.
`unbound` means it has no receiver and `_` is unavailable.

Free functions are implicitly `unbound`. Functions declared directly in a type
are implicitly `bound` to that type. Either word may be stated explicitly when
intent should remain visible:

```zax
Utilities :: type {
  compare final : (result : Boolean)(
    lhs : String readonly &,
    rhs : String readonly &
  ) unbound = {
  }
}
```

The type owns and qualifies `Utilities.compare`, but no `Utilities` instance is
passed to it.

`type of` inspects callable type information without evaluating an instance:

```zax
myValue : MyType
BoundPrototype :: alias type type of myValue.process
```

The type operation does not itself create a runtime callable waiting for a
receiver. The expression `myValue.process` creates the actual bound callable
value when one is needed.

Every `final` function has one fixed implementation and no replaceable
function-value slot per instance or type:

- `final bound` uses the receiver supplied by its instance-qualified call;
- `final once bound` also permits a receiverless type call using the containing
  type's Nothing instance;
- `final unbound` is one fixed receiverless implementation owned by its
  declaration path;
- `varying unbound` has ordinary per-instance replaceable storage when declared
  directly in a type;
- `varying bound` has replaceable receiver-capable storage and may install a
  compatible unbound, borrowed-bound, or ownership-backed implementation;
- `once varying unbound` has one replaceable slot for the complete type; and
- `once final unbound` adds neither storage sharing nor another call form and is
  a non-acknowledgeable intent error.

Receiver-capable storage may additionally state `unique`, `strong`,
`strong atomic`, `weak`, or `weak atomic`. These words describe the maximum
receiver-lifetime relationship the slot can store; an installed unbound or
borrowed target remains unbound or borrowed.

`weak` and `weak atomic` require a completely empty callable result contract.
Neither ordinary nor exceptional results are permitted because failed weak
promotion cannot manufacture a result. Complete invocation behavior is defined
by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#weak-invocation-is-conditionally-empty).

For new direct storage, declaration-side `final` resolves the omitted type-side
place stance to final. A `final` and an otherwise defaulted varying callable
therefore have different storage types even when their explicit result/input
prototype is the same.

Complete lambda expressions, callable storage capacity, installed binding kind,
and replacement behavior are taught by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#callable-prototypes-and-storage).

### Type-callable `once` functions

A `once` function declared inside a type has one type-owned implementation
shared by all instances:

```zax
MyType :: type {
  inspect final once : ()() = {
    // `?_` is false for MyType.inspect() and true for value.inspect().
  }
}
```

It is callable through either the containing type or an instance:

```zax
MyType.inspect()

value : MyType
value.inspect()
```

On a receiverless type call, `_` identifies the containing type's Nothing
instance and offers `copy`; `?_` is false. On an instance call, `_` identifies
the evaluated receiver and `?_` is true. A body that uses receiver state must
account for both call forms. `final` prevents reassignment under ordinary
function declaration rules.

`?_` is protected for this `once` receiver test. In a non-`once` bound function
it is a non-acknowledgeable intent error because an ordinary receiver is
certainly present. Postfix access binds first, so `?_.` dereferences `_` and
applies the ordinary selected `?` operation to the result.

The special constructor family selects the type's Nothing policy:

```zax
+++ final once : ()() = default
+++ final once : ()() = trap
+++ final once : ()() = {
  // Prepare dedicated custom Nothing storage.
}
```

The declaration takes no inputs, is not directly callable, and does not create
an ordinary zero-input constructor. Complete policy behavior and scoped
defaults are defined by
[Zax Nothing instances](nothing-instances.md#select-the-types-nothing-policy).

For a language-generated `once` function:

- omission permits demand-driven generation;
- `= default` explicitly requests the language-defined implementation;
- a declaration body supplies the owner's implementation; and
- `= forbidden` disables the exact signature.

This rule establishes type-callable function declaration and replacement. It
does not establish global or `once` value initialization, concurrency, teardown,
capture, or generic-specialization behavior. Invocation behavior is defined by
[Zax function invocation](function-invocation.md#type-and-instance-calls-to-once-functions).

## Identifier namespace and paths

Each lexical scope has one ordinary identifier namespace. This prevents
context-dependent type-versus-value lookup and makes collisions visible.

Structured cases remain distinguishable:

- declarations may form one overload set when overload rules permit it;
- a forward anchor and matching completion form one declaration relationship;
- members occupy their containing entity's member scope;
- one non-generic type and a generic type family may share a name, as
  `Integer` and `Integer$(...)` do, because their spellings never collide; see
  [generics](generics.md#sharing-a-name-with-a-non-generic-type);
- operators are not ordinary identifiers; and
- flow labels are a separate, explicitly shaped category that does not share
  ordinary-identifier lookup.

Root availability is source-ordered. At a use, an ordinary root must already
have been declared, exposed, or forwarded. The compiler does not search later
source to rescue an unrecognized root.

Lookup examines lexical scopes from nearest to farthest. Declarations
ineligible to the source are filtered before a scope is selected. If none
remain, lookup continues outward. Once a scope supplies one or more eligible
declarations, lookup stops there and applies its ordinary unique, family, or
ambiguity rules.

Source order never breaks a tie. Several imported/exposed declarations may
retain one spelling and remain ambiguous on demand, while two direct
declarations in one scope are an immediate error unless an explicit category
combines them. Complete namespace/import provenance and visibility behavior is
defined by [Zax namespaces and modules](namespaces-and-modules.md).

### Naming intent

Type-like and value-like declarations use mandatory capitalization and casing
intent:

```zax
myType :: type { } // error: type-like declaration uses a value-like name
MyValue := 0       // error: value declaration uses a type-like name
```

The compiler enforces mechanically knowable intent. Lints may advise on acronyms,
discouraged but legal shapes, or other conventions. Editors and spelling tools
may help with dictionary and domain terminology that the compiler cannot decide.

### Qualified paths

A qualified path resolves deterministically:

```zax
value : Node.Node
```

The first component is resolved lexically once. Each remaining component is
resolved only inside the entity selected by the preceding component. A missing
later component does not make lookup backtrack to another outer candidate for
the first component.

Aliases and stable roots preserve access when another name would be hidden:

```zax
OuterNode :: alias type Node
value : OuterNode.Node
```

```zax
value : Module.Node.Node
```

Imports can similarly use aliases. A path does not excuse a collision in its
root name.

### Pending path suffixes

A suffix may remain pending when an already resolved prefix names an incomplete
declaration:

```zax
MyType :: forward type

value : MyType.SubType
```

`MyType` resolves immediately to that forwarded declaration and remains fixed.
If completion of `MyType` later discovers `SubType`, the pending suffix resolves
inside the completed declaration. If `SubType` never appears, the path is
unresolved when the dependency graph must be finalized. The compiler does not
retry another outer `MyType`.

An explicit nested forward may categorize a needed member earlier:

```zax
MyType :: forward type
MyType.SubType :: forward type
```

It is not required merely because the containing type was incomplete when the
path was encountered. Operations requiring completed size, layout, overload
sets, or initialization behavior remain pending until enough information is
available.

This is dependency-directed later resolution, not speculative rebinding.

### Exact aliases and property overlays

Alias declarations add names without constructing runtime values:

```zax
MyReadView :: alias type MyType immutable readonly final &
MyUnionAlias :: alias union MyUnion
MyVariantAlias :: alias variant MyVariant
activeHandler :: alias variable handler
Tools :: alias namespace Module.SharedTools
MyMapping :: alias reshape ExistingMapping
```

Each category preserves its target:

- `alias type` preserves an ordinary type's canonical identity;
- `alias union` preserves a union's canonical identity and declaration
  category;
- `alias variant` preserves a variant's canonical identity and declaration
  category;
- `alias variable` denotes the same variable or approved polymorphic variable
  family, including the same varying function slot and a
  [generic function family](generics.md#naming-a-generic);
- `alias namespace` denotes the same namespace without granting reopening
  authority; and
- `alias reshape` denotes the same no-storage directional mapping.

Functions are variables, so there is no separate `alias callable` form.
`alias variable` creates no slot, capture, initialization, or compatible
wrapper:

```zax
handler varying : Callback = firstHandler
activeHandler :: alias variable handler

activeHandler = secondHandler
// handler now denotes the same replaced slot.
```

A type, union, or variant alias may state every property accepted where that
identity is explicitly used, including qualification, indirection, transfer
stance, and compatibility posture:

```zax
DeepView :: alias type MyType readonly & deep
CopyView :: alias type DeepView writable copy
```

Resolution overlays from less local to more local:

1. inherit properties supplied by the original type and previous alias;
2. replace each axis explicitly stated by the new alias or use; and
3. fill every still-unresolved axis from the common language defaults.

Overlay changes the requested declaration profile rather than an existing
value. `CopyView` cannot bind to a source that does not provide writable access,
and no alias manufactures mutability, replacement authority, transfer support,
or structural compatibility.

Canonical identity is owned by
[identity types](identity-types.md#transparent-aliases). The meanings and
safety checks of overlaid properties remain with
[qualifiers](qualifiers.md), [transfer stances](transfer-stances.md), and
[structural compatibility](structural-shapes-and-compatibility.md#compatibility-posture).
Namespace/module and literal aliases are completed by their respective owners.

## Qualifier axes

Complete programmer-facing qualifier behavior is defined by
[Zax qualifiers](qualifiers.md). This section establishes the declaration-facing
axes and attachment boundaries.

Zax distinguishes three independent concerns:

| Pair | Governs |
| --- | --- |
| `final` / `varying` | `final` promises the place keeps the same value lifetime; `varying` permits another lifetime in that place |
| `mutable` / `immutable` | `immutable` promises the current value's contents do not change through ordinary safe behavior; `mutable` makes no such promise |
| `writable` / `readonly` | `readonly` promises this access path performs no change; `writable` may perform an otherwise permitted content mutation or place replacement |

In short:

- `mutable` does not promise stable contents;
- `immutable` does promise stable contents;
- `writable` means this path may perform an otherwise permitted change; and
- `readonly` means this path only observes.

`readonly` identifies the access-path promise without conflating it with
immutable state or compile-time constant evaluation.

### `final` and `varying`

`final` and `varying` appear in two distinct positions, and they answer two
different questions:

| Position | Question answered |
| --- | --- |
| Type-use side | Whether the underlying place is actually `final` or `varying` |
| Declaration-name side | Whether this declaration may exercise whole-value replacement authority |

This parallels the distinction between a mutable value and a readonly access
path. The complete capability-versus-permission model is owned by
[Zax qualifiers](qualifiers.md#type-side-truth-versus-declaration-side-permission).

For new direct storage, an omitted type-side stance resolves from the
declaration-name side:

```zax
foo final : Foo         // final storage
foo varying : Foo       // varying storage
foo : Foo               // varying by default
foo : Foo final         // final
```

A declaration-side `final` may restrict replacement through a varying place, but
a declaration cannot claim more authority than the place actually provides:

```zax
foo final : Foo varying   // legal: restrict this declaration's replacement
foo varying : Foo varying // legal: retain replacement authority
foo final : Foo final     // legal
foo varying : Foo final   // error: access cannot exceed underlying capability
```

Compatible repetition is legal. This supports aliases, generic substitution,
generated declarations, and deliberate explicitness. A linter may report visible
redundancy.

A `final` place is nonreplaceable, not immutable:

```zax
foo final : Bar = makeBar()

foo = other       // ordinary replacement is unavailable
foo.member = 200  // legal when Bar and this access are mutable and writable
```

Place truth survives references and captures:

```zax
source final : Foo = makeFoo()
stableView : Foo final & = source
```

Normal replacement through `stableView` remains unavailable because the
referenced place is final. A type-use qualifier cannot misreport a referent's
actual stance:

```zax
source varying : Foo = makeFoo()
trackingView : Foo varying & = source

falseView : Foo final & = source // error: source's place is varying
```

A same-place alias may still narrow its own replacement authority through the
declaration-name side:

```zax
restricted final : Foo varying & = source
```

`source` retains replacement access. `restricted` cannot initiate replacement,
but its type use still truthfully records that another path may replace the
referent.

Declaration-side replacement permission must survive aliases, argument mapping,
results, and captures. A declaration-final path cannot regain replacement
authority merely by being supplied to a callable whose referent type remains
varying.

An omitted type-side stance on a reference inherits the actual referent-place
truth. A reference to a varying place therefore remains bound to that place and
may observe its completely established successor resident instance. It cannot
misreport the place as final. See
[Zax qualifiers](qualifiers.md#varying-places-with-immutable-lifetimes) and
[Zax lifetimes and references](lifetimes-and-references.md#references-never-rebind).

The protected `is final` query reports the resolved type-use or referent-place
truth, not this declaration's replacement permission.

A pointer may be repointed according to its pointer contract. A reference never
rebinds; assignment through it acts on its fixed referent place.

Copying by value creates a new place with its own declaration stance.

### `mutable`, `immutable`, `writable`, and `readonly`

A mutable value can be viewed through readonly access:

```zax
value : Foo mutable

writer : Foo writable & = value
reader : Foo readonly & = value
```

`reader` cannot modify the value, but `writer` or another permitted alias may.

An immutable value can provide readonly access:

```zax
value : Foo immutable
reader : Foo readonly & = value
```

No access may modify the underlying value. An immutable value cannot provide a
writable view.

The unqualified access default is writable everywhere; no directive changes it.
Explicit `writable` remains useful where a positive capability must be stated,
selected, reflected, or restored. Defaults fill only unresolved axes and never
override qualifications supplied by a source, referent, or resolved type.

### Capability conversions

An ordinary conversion may remove permissions or request weaker access:

- immutable values may provide readonly access;
- writable access may be viewed as readonly;
- readonly access may not become writable; and
- mutable or readonly values do not thereby become immutable.

Type-side final/varying truth is preserved for an alias of the same place, while
the declaration-name side may narrow that alias's own replacement permission. A
new by-value destination, constructed result, `copy`, or `move` destination resolves
its own independent place stance.

Unsafe qualification changes through `unsafe cast` are defined by
[Zax conversions and casts](casting.md#qualification). Reference projection and
capture projection remain later design. They must preserve the constraints
established by [Zax qualifiers](qualifiers.md).

### Declaration transfer stance

A value declaration may state how the completed value is ordinarily offered to
future consumers:

```zax
template : Document deep
resource : Resource move
stable : Resource copy
```

Omission resolves to `copy`:

```zax
ordinary : Resource
// The declaration's ordinary transfer stance is `copy`.
```

Transfer stance appears after the complete type use. It is one property of the
complete value, not another independently repeated qualifier layer and not
another declaration-name-side axis:

```zax
resource : Resource mutable writable varying & move
```

A use-site `as copy`, `as deep`, `as move`, or `as last` may restate the stance
for one consumer. Ordinary reference passing aliases a value and is not itself a
`copy` or `move`.

When member access starts from a by-value declaration, that declaration's stance
is the default for a member that has no explicit stance of its own:

```zax
package : Package move

ship(package.label) // `label` offers `move` unless it declares another stance
```

A reference declaration is different. It is an alias to an existing place, but
the alias has its own stance rather than silently inheriting destructive intent
from the declaration used to initialize it:

```zax
owner : Buffer move
alias : Buffer mutable writable & = owner

consume(alias)         // alias offers `copy`
consume(alias as move) // explicit `move` through this alias
```

The alias reaches the same storage but has its own declaration stance. Complete
projection, receiver, fallback, and source-state behavior is defined by
[Zax transfer stances](transfer-stances.md).

An inferred declaration may adopt the concrete value type and transfer stance of
its initializer:

```zax
makeResource final : (
  result : Resource move
)() = {
  // ...
}

resource := makeResource()
```

Here `resource` may infer both `Resource` and the produced `move` stance. It does
not infer `Resource &` or `Resource *`: stance does not say whether a declaration
is a value, reference, or pointer. Those shapes remain explicit or require
future dedicated inference rules.

This design does not classify broader capabilities such as sendability,
synchronization safety, pinning, known layout, copying, or interior freezing.
Future reviews may represent them as qualifiers, selectable operations,
reflection, concepts, compiler-recognized guarantees, type-definition contracts,
or combinations. The declaration model neither requires nor precludes those
choices.

## Operator phrase declarations and type parameters

An [operator phrase](operator-phrases.md) is declared exactly like a symbolic
operator, with its exact words quoted and its fixity stated:

```zax
Chicken :: type {
  operator post unary 'cluck loudly' final : (
    result : Chicken
  )() readonly = {
    // `_` is the receiver operand.
  }
}
```

The operator declaration is both the phrase-form declaration and its
implementation. Zax has no separate source-level phrase-form declaration and no
phrase `forward` form, so there is nothing to introduce ahead of the body.

Custom phrase implementations are receiver-owned. There are no global custom
phrase declarations, and a module may not add a natural phrase to a type it does
not own. Operand holes come from fixity and the callable prototype, never from
substitution inside the quoted words.

The complete phrase feature, including exact finite word sequences, source
interpretation, fencing, and the receiver-oriented workaround for a phrase that
no type can own, is taught by
[Zax operator phrases](operator-phrases.md).

### Type parameter slots and type arguments

A prototype may declare a **type parameter slot** completed by a concrete type
identity:

```zax
Source :: type {
  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
  }
}

converted := source as DestinationType
```

A **type argument** has no runtime storage or lifetime and is not evaluated at
runtime. It may determine a value result type, as it does for `as`. A type
passed as an argument is one of the
[two forms of generic](generics.md#two-forms-of-generic): the declaration is
processed once per distinct type, and the caller always writes the type.

Type category and unused intent remain independent concerns:

```zax
# : Value                 // runtime value is supplied; no body binding
value # : Value           // binding exists; unused use is intentional
DestinationType : type    // concrete type argument; no runtime value
```

The middle form is the unread-name marker from
[names the body may leave unread](#names-the-body-may-leave-unread). It does not
change `DestinationType` into a runtime value.

A value receiver may accept a type argument without that argument contributing
receiver discovery:

```zax
MyReceiverType :: type {
  operator binary 'for' final : (
    result : ResultType
  )(
    SubjectType : type
  ) = {
  }
}

schema : MyReceiverType
result := schema for SomeType
```

### Type-qualified operators

A concrete type identity may supply type-owned qualification and discovery for
an operator without becoming a runtime receiver:

```zax
MyType :: type {
  operator pre unary 'custom type info for' final : (
    result : MyCustomTypeInfo
  )() unbound = {
    // `_` is unavailable.
  }
}

info := custom type info for MyType
```

`MyType` anchors lookup in declarations owned by `MyType`. The declaration is
ordinary `unbound`, has no receiver slot, and adds no runtime storage or
lifetime for the type identity.

One operator may instead deliberately support both an instance-qualified call
and a type-qualified receiverless call by using ordinary `once`:

```zax
MyType :: type {
  operator pre unary 'inspect' final once : ()() = {
    if ?_
      inspectInstance(_.)
    else
      inspectType()
  }
}
```

Type-qualified operators use `unbound` when no receiver exists and `once bound`
when both type-qualified and instance-qualified routes are part of the
declaration.

A type-qualified operation is not inherently compile-time. It may execute at
runtime and return a runtime value:

```zax
instance := factory create MyType
```

When it runs follows the ordinary
[execution policies](compile-time-code.md#letting-a-function-decide-execution-policies);
a phrase adds no special execution rule. For a
non-generic declaration, the enclosing type name identifies the owner inside
its body. Inside a generic type's body, the current instantiation, written in
full as `MyVector$(Element)`, anchors discovery the same way; see
[generics](generics.md#inside-a-generic-types-body). Aliases and qualified
type-identity lookup as discovery anchors remain future reflection work.

### Literal operators

A literal operator declares one quoted lower-case phrase word, one `String`
payload input, and one value result:

```zax
MyType :: type {
  operator literal 'cstyle' final : (
    result : MyType
  )(
    payload : String immutable readonly final &
  ) unbound = {
  }
}

myValue := MyType.cstyle'payload'
```

`final` gives the declaration one fixed implementation. `unbound` states that
type ownership supplies qualification but no instance receiver. `once` is
neither needed nor legal in this `final unbound` combination. The
`operator literal` category is available only through compile-time literal
source.

A literal has no instance receiver. Type ownership supplies a qualified path,
not instance-based discovery. Future alias/import behavior may expose a short
prefix, but the expected result never searches a type for its literal
declarations.

A generic literal may declare an `uncommitted` scalar result. The payload is a
compile-time input, so the suggested result may depend on it:

```zax
operator literal 'h' final : (
  result uncommitted : UInteger
)(
  payload : String immutable readonly final &
) unbound = {
}
```

`UInteger` is a suggested specialization rather than the invocation's final
type. Compile-time processing selects one concrete result prototype before the
body is processed and invoked, once per distinct payload, as for any
[generic](generics.md#when-code-is-processed-once-per-input). The body and every
caller-facing rule then see only that concrete type; see
[literal source and operators](literal-source-and-operators.md#every-invocation-has-one-concrete-result).

Complete payload, qualification, ambiguity, specialization, merge, join, and
required-execution behavior is defined by
[Zax literal source and literal operators](literal-source-and-operators.md).
Exact literal aliases, visibility, and forwards are defined by
[literal source and operators](literal-source-and-operators.md#lookup-aliases-and-forwarding);
general import/export behavior is defined by
[namespaces and modules](namespaces-and-modules.md).

### Bounded private eligibility

A private type member is eligible only from the owning type's permitted private
context. Outside that context it is *ineligible* rather than merely a worse
candidate, so it is removed before callable preference and cannot defeat, tie, or
block a public operation:

```zax
Chicken :: type {
  weigh private final : (result : Grams)() readonly = {
  }

  weigh final : (result : Kilograms)() readonly = {
  }
}
```

Inside `Chicken`, both declarations are eligible and compete under ordinary
preference: visibility is not match quality among eligible candidates. Outside
`Chicken`, only the public declaration is eligible. Private-only declarations
therefore never change the meaning or validity of source whose caller can see
only public operations.

The visibility modifier follows the declared form rather than preceding
`operator`. Because word-spelled source may present several structurally complete
readings, this rule is what lets one phrase source line be ambiguous inside a
type and unambiguous outside it; that worked scenario is taught by
[operator phrases](operator-phrases.md#visibility-and-private-phrase-eligibility).

Complete visibility behavior, including nested types, friendship, modules, and
reflection, remains future visibility work.

## Assignment and overload selection

Operators occupy language-recognized operator categories rather than the
ordinary identifier namespace. User-defined nonliteral declarations are
receiver-owned as defined by [Zax operators](operators.md); literal declarations
are the receiverless exception. An arbitrary identifier or punctuation sequence
does not become an operator through ordinary name declaration.

An overloadable operator is a callable operation. User-defined operators may have
domain-specific effects and result shapes that are unusual outside their domain.
Exact forms and precedence are defined by the
[operator catalog](operator-catalog.md); multi-component tree declarations are
defined by [mixfix operators](mixfix-operators.md).

For:

```zax
consume(a = 1)
```

the compiler:

1. resolves `a`;
2. selects a viable `=` candidate for the qualified operands;
3. invokes it; and
4. maps its result to `consume` if the result shape is accepted.

No operator can introduce an unresolved operand as a declaration.

Protected ordinary intrinsic assignment evaluates the existing destination once,
updates its mutable value through writable access, and returns writable access to
that destination:

```zax
updated : Integer writable & = destination = source
```

This supports right-associated assignment chains when each selected result binds
to the next destination. The returned access is discardable, so
`destination = source` is also a complete statement; see
[Zax operators](operators.md#discardable-access-from-assignment-forms). Custom
`=` overloads may return another result shape.

Generated same-type `copy` assignment instead returns readonly `copy` access to
the assignment receiver `_`. That is sufficient for right-associated assignment
without granting another writable path:

```zax
first = second = third
```

Its exact qualifier-complete signature family and returned `_` are defined by
[construction, replacement, and destruction](construction-and-destruction.md#generated-copy-construction-and-assignment).

Generated and declared `=` operators participate in ordinary candidate
selection with their qualifier requirements. They operate within the current
resident lifetime and gain no replacement authority merely from the token.

Complete replacement uses protected `.=` instead. It requires a type-side
varying destination, declaration-side varying replacement permission, and a
writable path. The old value may be mutable or immutable. The compiler-owned
lifecycle skeleton may select a contextual
[`+++ replacement` constructor](construction-and-destruction.md#custom-replacement)
or use ordinary destruction/construction fallback. User-defined code cannot
overload `.=` or replace that skeleton with an ordinary operator body.

A domain-specific `=` candidate that accepts a final or readonly left operand
may remain selectable because the token itself is not assigned conventional
meaning in every scenario:

```zax
foo final : Bar = makeBar()
result := foo = other // requires a matching non-replacement candidate
```

Compound assignment, increment, decrement, and other operators likewise require
already declared operands and use ordinary selection.

Declaration initialization remains reserved:

```zax
foo : Bar = source
```

The `:` introduces `foo`; user code cannot overload that act. The initializer
selects construction or initialization behavior for the new `Bar`. Later
`foo = source` performs operator selection against an existing destination.

### Three roles of `=`

The same character appears in three different roles, and only one of them is
the assignment operator:

```zax
item : Item = source
// 1. Declaration initializer: constructs the declared place.

a: myFoo : MyFoo, b: bar: = makeValues()
// 2. Routing-group mapping: sends each producer result to its entry.
//    New declarations such as myFoo and bar are constructed.
//    An existing destination in the group receives ordinary assignment.

existing = source
// 3. Assignment operator: requires a live destination.
```

The assignment operator never constructs its destination. Its right side may
still construct values, such as a temporary, a producer's result slot, or the
pointee of a new allocation:

```zax
scheduled : MyValue * = @
reset scheduled
scheduled = @{ anotherArena }
// The pointer place is live and receives assignment.
// The new MyValue pointee is constructed on the right side.
```

To construct into a place that already exists, use `.=` instead. Routing
groups can also end in `.=`, which constructs or replaces every existing
destination; see
[Zax function invocation](function-invocation.md#construct-existing-places-with-a-dot-equals-group).

An uncommitted integer on the right of assignment may fill a concrete integer
input declared by an already discovered `=` candidate. The assignment receiver
bounds discovery; later use does not propagate an expected type backward into
the source expression. Candidate preference, post-selection range checking, and
failure are defined by
[Zax integer literals and realization](integer-literals.md#number-literals-filling-typed-inputs).

Replacement-constructor result forwarding is defined by the construction owner.
Exact results for other built-in operators, expression value categories,
overload ranking, conversion, replacement-hook input selection, and generated
operator sets remain later operator design. Protected `.=` does not compete with
ordinary `=` overloads.

## Non-value definitions

`::` introduces a family of non-value definition forms:

```zax
Point :: type { }
MyBits :: union {
  raw : U32
}
MyChoice :: variant {
  value : U32
}
Fruit :: enum { }
FriendlyName :: alias type ExistingType
MyUnionAlias :: alias union ExistingUnion
MyVariantAlias :: alias variant ExistingVariant
activeHandler :: alias variable handler
Tools :: alias namespace Module.SharedTools
MyMappingAlias :: alias reshape MyMapping
MyCount :: identity admit expose type U32
MyHandle :: identity restricted opaque type Integer
MyMapping :: reshape {
  sourceName: destinationName:
}
ModuleName :: import Module.Definition
TypeName :: forward type
UnionName :: forward union
VariantName :: forward variant
EnumName :: forward enum
valueName :: forward variable
NamespaceName :: forward namespace
ImportedModule :: forward module
MappingName :: forward reshape
x :: forward operator literal
PartialName :: forward partial
MyPointOps :: partial Point { }
PointOps :: alias partial Module.Geometry.MyPointOps
Module.Geometry.MyPointOps :: expose partial
Module.Geometry.MyPointOps :: own partial
```

`partial` adds declarations to an existing type, and `alias partial` gives a
partial another name. `expose partial` makes a partial visible in the rest of
the enclosing block. `own partial` appears in an import injection body and
includes a partial's storage in the imported type without making its names
visible there. All are defined by [Zax partials](partials.md).

The family does not imply one runtime behavior. In particular, `::` does not mean
"evaluated at build time." Build-time versus runtime evaluation depends on
functions, inputs, directives, and evaluation context. Build-time values use
ordinary `:` or `:=` bindings.

A named type becomes visible as an incomplete type before its body is resolved:

```zax
Node :: type {
  next : Node *
}
```

`type`, `union`, and `variant` introduce different body models while sharing the
non-value definition boundary:

- ordinary [type definitions](type-definitions.md) contain direct stored members
  plus behavior and metadata;
- [unions](unions.md) begin with untagged offset-zero lens declarations; and
- [variants](variants.md) begin with named managed alternatives.

The specialized owners define body organization, storage, lifecycle, and
validity. `::` only establishes that these are definitions rather than runtime
value declarations.

The self-name resolves and pointer representation is finite. Operations
requiring the completed size, layout, or member set remain pending until the
definition completes.

Functions and ordinary values use `:`, including values with anonymous types:

```zax
callback final : FunctionType = { }
value final : :: type {
  member : Integer
}

bits : :: union {
  byte : U8
  word : U32
}

choice : :: variant {
  text : String
  number : U32
}
```

Each anonymous `type`, `union`, or `variant` declaration creates one stable
compile-time identity. `type of` can give that identity a transparent alias.
Anonymous recursive self-syntax is not established.

`alias type`, `alias union`, and `alias variant` introduce another name for one
identity while preserving its declaration category. An alias definition may
also appear in a function body. Like any local declaration, it is visible from
that point to the end of its block:

```zax
countWords final : (result : Integer)(text : String readonly &) = {
  Entry :: alias type MyPair$(String, Integer)
  myFirst : Entry
  // ...
}

myLast : Entry   // error: Entry is local to countWords
```

`identity ... type` introduces a new identity represented by an existing type. Identity declarations
write one admission keyword (`admit` or `restricted`) and one surface keyword
(`expose` or `opaque`) before `type`; neither axis has an omission default.

Complete projection, admission, identity bridges, representation relationships,
and exposed/opaque behavior are defined by
[Zax identity types](identity-types.md).

`reshape` introduces a no-storage directional source-path-to-destination-path
map for explicit structural transformation. It has no runtime instance or
anchor. Complete behavior belongs to
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#reusable-mapping-with-reshape).

`enum`, `enum relaxed`, and `enum flags` introduce specialized integer-backed
identities. Their member prologue, backing eligibility, defaults, admission, and
body behavior are defined by [Zax enums](enums.md).

### Forward anchors

`forward` introduces a source-ordered name and declaration-category anchor before
its direct declaration or exact alias is encountered. An unrecognized root is
otherwise diagnosed without searching later source.

The available categories are:

```zax
TypeName :: forward type
UnionName :: forward union
VariantName :: forward variant
EnumName :: forward enum
valueName :: forward variable
NamespaceName :: forward namespace
ModuleName :: forward module
MappingName :: forward reshape
x :: forward operator literal
PartialName :: forward partial
```

`forward variable` includes one function variable or approved polymorphic
function family. Enum members need no separate forward because they can remain
pending suffixes below a forwarded enum. Union lenses and variant alternatives
likewise remain pending below their category-specific root anchors.
Receiver-owned nonliteral operators need no forward because their receiver type
is the root anchor.

The forward supplies no body, value, layout, member set, function prototype,
mapping, module instance, or initialization state. Dependent checks remain
pending. The matching completion must have the same scope, name, and category
and may be either a direct declaration or exact alias. `forward module`
completes through one import. `forward partial` completes through one
[partial](partials.md) declaration or exact `alias partial`. A partial
declaration adds to the type it extends and never completes a forward of that
type. When the extended type is still pending, for example behind a
`forward module`, the partial's checks wait until the type completes.

`forward union` and `forward variant` preserve their specialized categories.
`alias union` and `alias variant` may respectively complete them. An ordinary
`type`, `alias type`, enum, or alias to another category is a completion
mismatch.

Every forward must complete exactly once before module finalization. A matching
forward is legal even when no intervening source needed it; tooling may lint
that redundancy. A named type does not need `forward` merely to refer to its own
name inside its body.

Forwarding remains useful for out-of-order and mutually recursive names:

```zax
OtherNode :: forward type

Node :: type {
  next : OtherNode *
}

OtherNode :: type {
  next : Node *
}
```

The later definition completes `OtherNode`.

The declaration may be written physically inside another namespace when its
qualified path selects the real owner:

```zax
namespace A {
  namespace B {
  }
}

namespace C {
  A.B.PendingType :: forward type

  func final : ()(
    foo : A.B.PendingType
  ) = {
  }
}

namespace A.B {
  PendingType :: type {
  }
}
```

Ordinary root lookup makes `Module.` optional here because `A` resolves
unambiguously to `Module.A`. Writing
`Module.A.B.PendingType :: forward type` fixes that root explicitly.

Physical placement inside `C` does not introduce `C.PendingType`; an unqualified
`PendingType` there is still an error unless source declares an explicit local
alias. The qualified forward belongs to `A.B`, and the source writing it must
have declaration/reopening authority over that namespace.

Every containing component must already resolve as a namespace or matching
namespace forward. Missing parents are explicit:

```zax
Module.A :: forward namespace
Module.A.B :: forward namespace
Module.A.B.PendingType :: forward type
```

A qualified forward never manufactures intermediate namespaces. This
declaration requirement is stricter than an ordinary use whose suffix may remain
pending below one already resolved incomplete prefix.

The completion category must match:

```zax
Something :: forward namespace

Something :: type { // error: expected a namespace completion
}
```

The nearer wrong-category declaration does not make lookup skip outward seeking
another completion.

Direct infinitely recursive layout remains an error:

```zax
Node :: type {
  next : Node // error: infinitely recursive layout
}
```

Mutually recursive named types can forward each required name. A completion
never makes an already selected root retry lexical lookup.

### Anonymous recursive type syntax

Named types, unions, and variants use their own incomplete names for
self-reference. Anonymous recursive syntax for any of those categories is not
established by this design.

Anonymous recursive types, recursive-type identity, and dependency algorithms
remain later type work. Namespace/module completion and visibility are defined
by [Zax namespaces and modules](namespaces-and-modules.md).

## Declaration contexts

The binding model applies beyond local statements. Parameters, results,
captures, stored members, type names, imports, and flow headers provide their own
declaration contexts. They share name, type, initialization, and qualifier
concepts without all becoming statements.

### Flow-control initialization

A flow-control initializer accepts any effective statement. A binding introduced
there becomes visible after its own initializer completes and remains visible to
later initializer operands, the condition, the applicable clause bodies, the
corresponding false or `else` clauses, and the post operation, then leaves scope
after the complete flow statement:

```zax
if result := tryValue() ;; result.isValid() ;; recordAttempt(result)
  use(result)
```

`result` is visible to the condition, the body, and the post operation
`recordAttempt(result)`, and is destroyed when the complete `if` exits.

An explicit block inside a composed initializer keeps its own nested scope, so a
binding it introduces is destroyed at the block's `}` and does not escape to the
condition, body, or post. A sibling initializer operand introduced outside the
block belongs to the enclosing flow-header scope:

```zax
if {
  token := hello()
  goodbye(token)
}; value := compounding() ;; value < 1 {
  use(value)
  token = 5 // error: token belonged to the completed inner block
}
```

Here `token` belongs to the completed inner block, while `value` is a header
binding visible through the condition and body. The flow-header schema, `;;`
section roles, and phase execution are owned by
[core flow control](core-flow-control.md); token spacing and mandatory layout are
owned by [source structure](source-structure.md).

### Scoped-resource entries

A declaration in a `using` resource list becomes visible after its initializer
completes and remains visible to later entries and the body:

```zax
using (
  connection := connect(),
  grant := connection.acquireGrant()
) {
  use(connection, grant)
}
```

These declarations belong to the `using` header rather than its body. Their
owned values are destroyed only after the complete resource disposal phase.

Several results may introduce bindings through source-result labels:

```zax
using (
  resource: myResource:,
  grant: myGrant: = acquirePair()
) {
  use(myResource, myGrant)
}
```

The first label in each pair selects the producer result; the second introduces
the new ordinary binding. A selected result may instead initialize a complete
typed destination declaration:

```zax
using (
  resource:,
  grant: disposalGrant : MyCarryingGrant = acquirePair()
) {
  use(resource, disposalGrant)
}
```

`grant:` selects the source result. The caller-owned
`disposalGrant : MyCarryingGrant` destination determines the value and
operations that participate in `using`. Positional typed declarations do not
introduce new names for a several-result producer in this context. Complete
result mapping belongs to
[function invocation](function-invocation.md#result-routing-groups), and
complete entry ownership, disposal, and lifetime belong to
[Zax `using`](using.md#resource-entries).

### Flow labels and the ordinary namespace

A flow label is a separate, explicitly shaped name category from the ordinary
identifier namespace. Because the two categories do not share lookup, the same
spelling may name both a binding and a label without ambiguity:

```zax
while outer: outer := 0 ;; outer < 100 ;; ++outer {
  ++outer
  break outer:
}
```

Ordinary binding shadowing is checked only against ordinary bindings, and label
shadowing only against labels. The one-level `shadowable` permission model
described above applies to each category independently: an outer label may grant
one nested reuse of its spelling, and the inner label must itself say
`shadowable` to permit a further reuse. Complete flow-label spelling, placement,
target eligibility, and `:` reference are owned by
[core flow control](core-flow-control.md#flow-labels-and-transfer-targets).

Case labels remain in this separate label namespace but are visible throughout
their containing switch for applicable `continue` and `goto` transfers. They do
not introduce ordinary bindings, and sibling cases cannot reuse one spelling.
Whether a case label or same-named outer label is eligible depends on the
transfer keyword and source position; complete behavior belongs to
[core flow control](core-flow-control.md#flow-labels-and-transfer-targets) and
[switch, case, and default](switch.md#labels-and-target-visibility).


### Stored members

A stored-member initializer runs once per actual containing instance. A storage
qualifier such as `once` may change how many storage instances exist and when the
one instance is initialized. It does not make an ordinary member initializer run
once merely because it appears once in a type definition.

An ordinary type body's storage, fixed behavior, varying callable slots,
type-owned declarations, and no-storage metadata are distinguished by
[Zax type definitions](type-definitions.md#what-a-type-body-can-contribute).
Union lenses are not ordinary stored members, while variant alternative
declarations create conditionally resident payload paths.

### Default parameters

An input parameter may declare a default expression:

```zax
connect final : ()(
  host : Host,
  attempts : Integer = 3
) = {
}
```

The parameter is a declaration context. Exactly one source establishes it for a
call:

- an explicit argument; or
- its declared default after all explicit value-producing arguments complete.

The new parameter name becomes visible to later parameter defaults only after
its own binding completes. It is not visible in its own default.

Complete omission syntax, default evaluation order, and call-contract ownership
are defined by
[Zax function invocation](function-invocation.md#omitted-inputs-and-defaults).

### Multiple results

Results are specialized declaration contexts and ordered output obligations. A
result declaration does not default-construct a body local merely because it
names a type:

```zax
make final : (
  result : Item
)() = {
  return source
}
```

`return source` directly constructs the result slot. A result initializer opts
into construction before body entry:

```zax
make final : (
  result : Item = :
)() = {
  result.name = "example"
}
```

An exceptional result places `except` on the result declaration:

```zax
read final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  // ...
}
```

`except` belongs to this callable result declaration, not to `MyFailure`.
Success publishes the ordinary `value` result; producer `except` or forwarding
constructs and publishes `failure` instead.

Exceptional results accept no declaration initializer and may not be manually
constructed before selection. Their one construction point remains visible in
producer `except` or explicit forwarding. Complete outcome, handling, and
cleanup behavior is defined by
[Zax exceptional result flow](except.md).

A result's initializer decides how that slot is constructed. Once the result
exists, the stance written on the result declaration decides how later consumers
see it:

```zax
make final : (
  result : Item move
)() = {
  return source as copy
}
```

`source as copy` controls construction of `result`. After construction,
ordinary uses of `result` in the body offer `move`, and the caller also receives
a `move`-stanced result.

Forwarding through another result declaration crosses both contracts: the
producer's stance fills the new slot, and the forwarding result declaration
controls what its caller receives.

An omitted owned by-value result still has implicit `copy`. At its structurally
final mapping boundary, invocation may require explicit intent when offering
`last` could materially change the accepted consumer. Reference results are
excluded because their slots do not own the referent. Complete behavior is
defined by
[Zax function invocation](function-invocation.md#result-mapping-and-terminal-opportunity).

At a result-routing site, adjacent `name:` selects the result label and may
introduce a same-named inferred binding:

```zax
number:, text: = produce()
```

The labels must exist in the selected callable prototype. Declaration spacing
expresses a different intent:

```zax
number :, text: = produce() // error: incomplete declaration-like intent
```

A complete typed capture introduces ordinary declarations:

```zax
number : Integer, text : String = produce()
```

That form consumes results positionally. A source-result label may instead select
the result before introducing an inferred or typed destination:

```zax
number: sum:,
text: description : String = produce()
```

`number:` and `text:` select producer results. `sum:` introduces an inferred
destination, while `description : String` introduces a typed destination
initialized from the selected `text` result. This source/destination order is the
same in declarations, call routing, return routing, and `using`.

New declarations and existing destinations may coexist:

```zax
number:, existingText = produce()
```

The first result initializes a new binding. The next result performs ordinary
assignment into `existingText`. Mapping is ordered and nontransactional. A
later panic does not roll earlier work back or unwind to the caller; the blocked
operation resumes after a matching repair or the process crashes gracefully.

Ending the group with `.=` instead constructs into existing places. Every
existing destination receives `.=`, so a `.=` group never assigns:

```zax
first: _.a, second: mySecond : MySecond .= makeTwo()
// Inside a constructor: `_.a` is constructed from `first`;
// the new `mySecond` is constructed from `second`.
```

Complete `.=` group behavior is defined by
[Zax function invocation](function-invocation.md#construct-existing-places-with-a-dot-equals-group).

Duplicate introduced names remain errors. Ordinary declaration visibility,
same-scope redeclaration, and declaration-versus-assignment rules continue to
apply. Complete result labels, routing, omission, construction order, and
completion are defined by
[Zax function invocation](function-invocation.md#result-labels-and-acknowledgement).

A `catch` destination is an ordinary declaration scoped to its handler body:

```zax
value := operation() catch failure: localFailure: {
  report(localFailure)
  return
}
```

The enclosing success declaration becomes visible only after the success
initializer completes, so it is unavailable in its exceptional handler.

`using` applies these rules in a mapping-capable resource list. It permits
same-name source-label capture and an explicit source/destination pair, including
a complete typed destination declaration, but rejects positional introduction
of several named resource bindings. See
[Zax `using`](using.md#capture-by-source-result-label).

## Documentation attachment

Documentation attaches to the complete declaration construct:

```zax
/// Describes x.
x := makeValue()
```

```zax
/// Describes the produced pair.
number:, text: = produce()
```

The second block documents the complete multi-binding declaration rather than
only its leftmost name. Documentation before a flow statement attaches to the
flow statement, not automatically to a declaration inside its header.

Grouping, placement, duplicate attachment, and other general attachment rules
are owned by [Zax source structure](source-structure.md).

## Diagnostics

Diagnostics should distinguish:

- assignment to an unresolved name;
- an unrecognized root from a recognized but incomplete forward anchor;
- a forward category mismatch, duplicate completion, or missing completion;
- a declaration with neither an explicit nor inferable type;
- same-scope redeclaration;
- shadowing without permission from the hidden declaration;
- use of an ordinary binding before initialization completes;
- an implicit immutable reference to a varying place where explicit `varying`
  acknowledgement is required;
- a same-place alias whose final/varying stance conflicts with its referent;
- duplicate qualifier tokens at one syntactic point;
- conflicting explicit place qualifiers;
- a type mismatch or unavailable initializer;
- an unavailable operator candidate for the qualified operands;
- an attempt to regain replacement, writable, or mutability capabilities not
  supplied by the source;
- an alias property profile that an actual source cannot satisfy;
- an exact `alias variable` from a compatible visible callable wrapper;
- assignment whose installed callable binding exceeds the destination capacity;
- `once final unbound`, whose `once` has no remaining effect;
- direct infinitely recursive type layout;
- use of an incomplete type where completed layout is required;
- ambiguous multi-result mapping;
- implicit instance-member access without `_.`;
- an unread named binding that was not acknowledged with `#`;
- a declaration form used in a source position that does not accept it;
- declaration-like spacing that conflicts with label intent;
- duplicate bindings introduced by a result-routing construct; and
- positional introduction of several named `using` results where source-result
  labels are required;
- use of an unconstructed result slot as a live value;
- ordinary assignment to a place that has not been constructed yet;
- a `.=` routing group outside statement position;
- an `except` marker outside a callable result declaration;
- an exceptional result with a prototype initializer or manual early
  construction;
- a success declaration used from its exceptional handler before initialization
  completed;
- an identity declaration missing either its admission or surface keyword; and
- conflicting `admit`/`restricted` or `expose`/`opaque` intent;
- a type-qualified call to a function that is not `once`; and
- a receiverless `once` call that selects an unavailable receiver stance or
  performs a trapping/unprepared Nothing access.

Exact identifiers, wording, and presentation remain later diagnostics design.

## Formatting

Formatting should make declaration intent visible:

```zax
inferred := value
explicit : Type = value
stableValue final : Type = value
anonymous final : :: type { }
MyCount :: identity admit expose type U32
```

A formatter may canonicalize `name : =` to `name :=`. It must preserve a
missing anonymous name versus an explicit `#`, even though those ordinary
declarations have the same value behavior. It must also preserve `#` between a
name and its type, and a statement `name #`, because those acknowledge an
unread binding rather than removing its name. It must also preserve the separation
among binding, value, access, and referent-place qualifiers and must not silently
resolve contradictory source intent. It may normalize qualifier ordering and
spacing but must not add or remove explicit qualifiers.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, compatibility
promise, implementation mapping, or conformance specification.

It establishes constraints that later work must preserve:

- complete inference may add rules without making later uses determine an
  earlier inferred declaration;
- complete inference must decide whether `:=` introduces a value or preserves a
  reference/pointer layer; an inferred value may adopt its producer's transfer
  stance, but stance does not imply reference shape, and `: & =` remains the
  explicit way to request a reference at the current conceptual depth;
- named functions and bound lambdas may use their defined recursive binding or
  self receiver without allowing executable ordinary self-initialization;
- function invocation may use declarations as inputs and result destinations
  without changing when a declared binding becomes visible;
- ordinary anonymous declarations with a missing name or explicit `#` retain
  equivalent value behavior outside mapping-specific contexts;
- `name # : Type` lets that scope leave the binding unread, while result `#`
  remains the caller's permission to omit a result;
- a statement `name #` acknowledges an existing binding this body did not
  declare and does not introduce a declaration;
- result slots and explicitly controlled constructor members may delay
  construction without creating an initialize-later form for ordinary local
  declarations;
- named non-value definitions may expose incomplete self-names without making
  ordinary value initializers self-referential;
- constructors and lifetime policies must preserve the distinction between
  default and direct initialization, including automatic and explicit member
  construction;
- a declaration's `=` remains part of declaration syntax, while `.=`
  constructs into a place that already exists;
- transfer, ownership, and qualifier design must preserve independent binding,
  declaration stance, value, place, and access capabilities;
- operator design must not permit operator overloads to introduce unresolved
  names;
- flow-control design must preserve header-binding scope, nested block scope, and
  the separate flow-label category described here;
- scoped-resource design must preserve source-ordered header binding visibility
  and explicit source-result labels for named several-result enrollment;
- module and name-resolution design must preserve one lexical identifier
  namespace, fixed path roots, and pending suffix resolution; and
- structural shape and compatibility preserve direct resident-member names,
  declaration order, nested identity, type-side qualifications, and physical
  versus alternate access-path distinctions as defined by
  [Zax structural shapes and compatibility](structural-shapes-and-compatibility.md);
  defaults and initializers affect construction rather than completed stored
  shape, while future reflection reports the resolved distinction; and
- [generics](generics.md), [composition](composition.md), and
  [partials](partials.md) must preserve the explicit
  identity-declaration integration owned here and the behavior owned by
  [Zax identity types](identity-types.md); and
- future global and `once` lifetime work must preserve type-callable `once`
  functions without treating the accepted call surface as a global-value
  initialization rule.
