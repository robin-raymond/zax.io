# Zax construction, replacement, and destruction

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing value construction, reconstructive replacement, and destruction; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Ordinary constructors and destructors; contextual/explicit constructor participation; automatic and explicit member lifecycle operations; the uses of `.=`, including first construction of explicitly controlled members and the first-construction state rule; construction asserted at an opaque operation; construction packets; lifecycle declaration states; qualifier-complete generated `copy` families; generated assignment result; complete reconstructive replacement with protected `.=`; replacement constructors, resource retention, packets, and results; construction/destruction authority; wrapper-owned contained reconstruction with `.=`; optional and variant integration at the common lifecycle depth; automatic local, body, and flow-header lifetime ending and destruction order across normal and abrupt scope exits; proof and checked-access boundaries for conditionally live storage; manual and delayed construction boundaries; lifecycle costs, diagnostics, and formatting |
| Does Not Own | Complete transfer meaning and fallback ([transfer stances](transfer-stances.md)); composition publication and forwarding ([Zax composition](composition.md)); complete [optional behavior](optional-values.md); complete [variant behavior](variants.md); unmanaged [union behavior](unions.md); integer realization and numeric-source candidate behavior ([integer literals and realization](integer-literals.md)); declaration/qualifier behavior ([declarations and bindings](declarations-and-bindings.md), [qualifiers](qualifiers.md)); shared invocation selection ([function invocation](function-invocation.md)); cohesive [exceptional result flow](except.md); `using` resource enrollment and structural disposal ([Zax `using`](using.md)); flow-transfer/post-operation behavior ([core flow control](core-flow-control.md)); [reference lifetime](lifetimes-and-references.md); or general [safety contracts](safety-and-analysis.md) |

## Mental model

Zax distinguishes three related states:

1. **Storage exists.** A suitably sized and aligned place is available.
2. **Member lifetimes exist.** Individual contained places may or may not
   currently contain live values.
3. **The complete enclosing lifetime exists.** Ordinary code may use the
   complete instance after construction finishes and before replacement or
   destruction begins.

Each instance lives within a
[life path](lifetimes-and-references.md#start-with-a-life-path). An
[instance place](lifetimes-and-references.md#life-path-instance-place-and-resident-instance)
is the stable typed position through which its complete resident instance is
reached. This document defines how lifecycle operations establish and end those
instances; the lifetime owner defines how references remain valid across those
events.

Allocation may obtain storage, but allocation alone does not construct a value.
Construction establishes a complete value in storage. Destruction ends a
complete value and its remaining member lifetimes. Deallocation is a separate
responsibility of the applicable storage or ownership policy.

Reconstructive replacement reuses the outer storage while recycling the
previous representation and resources into a new complete mutable or immutable
value lifetime.

### Nothing preparation is not ordinary construction

A compiler-provided Nothing instance is prepared for type-directed vacant
pointer and receiverless-call behavior without invoking an ordinary `T`
constructor:

```zax
MyType :: type {
  value : Integer
}

pointer : MyType *
// The pointer is vacant; no ordinary MyType was constructed.
```

Preparation recursively establishes readable member defaults where the member
kind supports them and panicking behavior otherwise. It does not establish an
ordinary complete enclosing `T`, satisfy its cross-member invariants, or
schedule its ordinary destructor.

The special `+++ final once` declaration instead prepares dedicated custom
Nothing storage:

```zax
+++ final once : ()() = {
  // Complete the custom Nothing representation before publication.
}
```

Its body takes no inputs, is not directly callable, and is distinct from an
ordinary zero-input constructor. Exact default, trapping, custom, and scoped
policy behavior belongs to
[Zax Nothing instances](nothing-instances.md#select-the-types-nothing-policy).
Global ordering, concurrent publication, failure, and teardown remain future
global/`once` lifecycle work.

Resetting a varying function value ends and dispositions its current callable
representation, including owned captures, then establishes the function type's
unavailable default state. It is lifecycle-aware and therefore distinct from
raw-pointer `vacate`, which deliberately performs no target disposition.

Replacement first completes the new callable, then dispositions the old
installed mode, then publishes one complete successor. Unbound and borrowed
targets release no receiver lifetime; unique, strong, atomic strong, and weak
targets release their applicable ownership or observation relationship. A panic
leaves the same replacement/reset operation blocked rather than exposing a
half-updated slot. Complete mode-specific behavior belongs to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#callable-transfer-reset-and-recursion).

### Construct into an existing place with dot-equals

A declaration constructs the place it introduces. Some places are introduced
in one location and constructed, or constructed again, somewhere else. `.=`
constructs a new value into such an existing place:

```zax
Session :: type {
  connection : Connection

  +++ final : ()(endpoint : Endpoint) = {
    _.connection .= [{ endpoint }]
    // The member starts empty in this constructor: first construction.
  }
}

make final : (
  result : Item
)() = {
  result .= source
  // The result slot starts empty: first construction.
}

message .= makeMessage("second")
// `message` is live: its old value ends and a new one takes its place.

optional .= [{ constructorInputs }]
// The wrapper stays; a new payload is constructed inside it.
```

`.=` has exactly four uses:

| Use | The place before `.=` | Taught in |
| --- | --- | --- |
| First construction of a constructor member | An empty direct member under explicit construction control | [Automatic and explicit member construction](#automatic-and-explicit-member-construction) |
| First construction of a result slot | An empty result slot | [Function invocation](function-invocation.md#opt-in-result-initialization) |
| Replacement | A live varying place | [Reconstructive replacement](#reconstructive-replacement) |
| Payload construction | A live optional or variant wrapper | [Wrapper-owned contained reconstruction](#wrapper-owned-contained-reconstruction-with-dot-equals) |

First construction means exactly what the same source would mean as a
declaration initializer, and it needs no replacement permission. Result slots
and explicitly controlled members are the only places that can be empty after
they are declared. The compiler decides whether a particular `.=` constructs or
replaces with the rule in
[How the compiler decides first construction](#how-the-compiler-decides-first-construction).

`.=` is compiler-owned and cannot be overloaded. Every `.=` expression
produces access to the place it constructed:

```zax
+++ final : ()(first : Buffer, second : Buffer) = {
  a := _.buffer .= first    // first construction: access to the buffer
  b := _.buffer .= second   // replacement: access to the same buffer
}

payload := optional .= value                  // access to the new payload
wrapper := variant .= [{ .text = "ready" }]   // access to the wrapper
```

A complete variant packet produces access to the wrapper, because its
zero-entry form constructs absence and has no payload. Replacing a complete
optional wrapper likewise produces access to the wrapper.

The access carries the qualifications of the path used to reach the place and
never more authority. A readonly result produces readonly access, and access
to a `final` place cannot be used to replace it.

Because the value does not depend on whether the place was empty or live,
chains are predictable. `.=` is right-associative, so in `a .= b .= c`, `a` is
always built from `b`.

The access is discardable, so `place .= source` is a complete statement; see
[Zax operators](operators.md#discardable-access-from-assignment-forms).

## Ordinary construction

### Constructor declarations

An ordinary constructor is a type-owned `+++` declaration:

```zax
Connection :: type {
  +++ final : ()(endpoint : Endpoint) = {
    // Establish the Connection.
  }
}
```

An ordinary constructor:

- has no result values;
- accepts zero or more ordinary input parameters;
- participates in ordinary parameter qualification and
  [callable selection](function-invocation.md#candidate-selection);
- receives `_` as the current instance under construction;
- may establish final and immutable state without `unsafe pliable`; and
- must establish a complete instance on every normal return.

Construction authority records the value's actual qualifications while allowing
the current instance to be established. It is not a general unsafe escape.

The complete instance's final and immutable guarantees activate only when
construction finishes. Before then, the compiler may know that some members are
live while others are not.

### Automatic and explicit member construction

Most members are constructed automatically before the constructor body runs.
A constructor takes over a member's construction by constructing it
explicitly in the body:

```zax
MyType :: type {
  a : A
  b : B = makeB()
  c : C
  d : D
  e : E

  +++ final : ()() = {
    _.c .= makeC()                                     // c is constructed here
    unsafe<opaque-construction>{ lowLevelFill(_.d) }   // d is constructed by lowLevelFill
    _.e = makeE()                                      // e already exists: assignment
    use(_.a)                                           // a already exists
  }
}
```

- `a`, `b`, and `e` are constructed automatically before the body, in
  declaration order. Each uses its own declaration: `b` is constructed from
  `makeB()`, while `a` and `e` use default construction.
- `c` and `d` are under **explicit construction control**. They start empty and
  are constructed where the body reaches them.

A member is under explicit construction control when the selected constructor
contains any of these for it:

- `_.member .= ...`, including `_.member` as a destination in a
  [`.=` routing group](function-invocation.md#construct-existing-places-with-a-dot-equals-group);
- `_.member.+++(...)`, the explicit call form that `.=` abbreviates;
- an `unsafe<opaque-construction>{ ... }` enclosure naming the member, described
  in [Construction by an opaque operation](#construction-by-an-opaque-operation);
  or
- a direct allocation `_.member = @...`, described below, which suppresses the
  member's declared pointee allocation.

What matters is whether such a step appears, not whether the member is used.
`_.e = makeE()` is ordinary assignment, so it does not take over construction.
That line performs two operations: automatic default construction of `e`, then
assignment. If `E` has no default constructor, the line is an error. Write
`_.e .= makeE()` to construct `e` once, directly.

Explicit construction is what allows a member whose type has no default
constructor:

```zax
Session :: type {
  connection : Connection

  +++ final : ()(endpoint : Endpoint) = {
    _.connection .= [{ endpoint }]
    // Same as _.connection.+++(endpoint)
  }
}
```

Explicit steps run wherever constructor-body control flow reaches them, in any
order. After a member's first construction it is live, and later operations
treat it like any other live member. A member whose type cannot be
default-constructed, with no initializer and no explicit step in the selected
constructor, is an error.

A pointer member allocates its pointee only when its declaration explicitly uses
an allocation initializer:

```zax
Container :: type {
  empty : Child * unique
  allocated : Child * unique = @
  maybeAllocated : Child * unique = @!
}
```

`empty` is vacant. `allocated` obtains storage and constructs `Child`
as part of automatic member initialization. `maybeAllocated` is vacant
when its storage request fails, and the enclosing construction may still
complete.

Each member's omitted arena comes from the current thread execution context when
that member's `@` operation executes. It does not inherit the arena that stores
the enclosing instance. A constructor that requires a particular child arena
accepts or otherwise receives it explicitly.

Composition does not flatten construction. A member declared `own` is still one
complete stored member, and the containing type still constructs it as a whole.
A construction packet may therefore supply a complete value for that direct
member:

```zax
Container :: type {
  data own : AnimalData
}

container : Container = [{
  .data = makeMouseData()
}]
```

The shorter names published from `data` become useful only after `data` exists.
They do not become entries in `Container`'s construction packet:

```zax
container : Container = [{
  .animal = "mouse"
  // error: `animal` is inside `data`, not a direct member of Container
}]
```

`AnimalData` must instead be established through one of its own constructors,
as `makeMouseData()` does in the valid example.

The same whole-member boundary applies to lifecycle behavior. `expose`, `via`,
and `= existing` cannot turn a member constructor, replacement
constructor, destructor, generated `copy`, or generated assignment family into
the corresponding lifecycle operation for the container. Such an operation
would establish or end only one member, not the container's complete physical
shape. A container lifecycle body may still invoke member lifecycle explicitly
when its ordinary lifecycle authority permits.

Complete data publication and the rejected reach-through form are taught by
[Zax composition](composition.md#construction-remains-whole-member-construction).

Like `_.member .= ...`, a direct allocation assignment to the current instance
places that member under explicit constructor control:

```zax
MyType :: type {
  foo : Foo * = @
  bar : Bar * = @

  +++ final : ()(
    arena : MyArena &
  ) = {
    _.foo = @{ arena }

    if condition
      _.bar = @{ arena }
  }
}
```

For this selected constructor:

- the presence of direct `_.foo = @...` suppresses `foo`'s automatic pointee
  allocation before body entry;
- the pointer member itself is initialized vacant, with its
  declaration-attached schedule ready to adopt an allocation;
- the first reached direct allocation fills that empty slot; and
- a later reached direct allocation dispositions the current allocation and
  installs its replacement.

Direct allocation assignment is available for any typed pointer destination.
The constructor-specific behavior here is only suppression: syntactic
`_.member = @...` tells the compiler not to perform that member's declared
automatic pointee allocation before body entry.

Every normal path may leave the pointer member vacant; the pointer resident
instance is still fully constructed. However, a declaration written `= @`
strongly signals automatic allocation. A selected constructor that may suppress
that allocation on a normal path requires:

```zax
intent<conditionally-unallocated-member>{
  if condition
    _.bar = @{ arena }
}
```

The acknowledgement confirms that `bar` may remain vacant. It does not permit
an indeterminate pointer representation.

### A second `.=` on a member is replacement

The first `.=` on an explicitly controlled member constructs it and needs no
permission, even for a `final` or immutable member. Once the member is live, a
further `.=` is [reconstructive replacement](#reconstructive-replacement) and
follows the ordinary replacement requirements:

```zax
MyType :: type {
  label : Label immutable writable varying
  view : Label readonly varying &
  fixed final : Label

  +++ final : ()(first : Label, second : Label) = {
    _.label .= first
    _.label .= second  // replacement: the old immutable value ends; allowed

    _.view .= first
    _.view .= second   // error: replacement through a readonly path

    _.fixed .= first
    _.fixed .= second  // error: replacement of a final place
  }
}
```

`immutable` describes the value during one lifetime, so it does not prevent a
replacement that ends that lifetime. `readonly` restricts the access path and
`final` fixes the place; either one blocks replacement. Result slots follow
the same rule.

Only direct members start empty. Construction does not flatten, so
`_.member.sub .= value` can only replace a live `sub` after `member` exists. An
optional or variant member is constructed as a whole wrapper; the payload form
requires a wrapper that already exists:

```zax
MyType :: type {
  choice : MyChoice

  +++ final : ()() = {
    _.choice.text .= "ready"          // error: the wrapper is not constructed yet
    _.choice .= [{ .text = "ready" }] // constructs the wrapper with that alternative
  }
}
```

Outside constructors every member is live, so `_.member .= value` in an
ordinary method is always replacement. Members inside a
[custom replacement constructor](#custom-replacement) also start live.

### How the compiler decides first construction

The same `.=` constructs an empty place and replaces a live one, so the
compiler must know which state the place is in. It uses a deliberately simple
rule so that every compiler accepts and rejects the same programs.

Branches that agree are accepted:

```zax
make final : (
  result : Item
)(
  fast : Boolean
) = {
  if fast
    result .= makeQuick()
  else
    result .= makeCareful()
}
```

A loop is rejected, even when it would construct only once:

```zax
make final : (
  result : Item
)(
  candidates : Item[]
) = {
  each candidate : in candidates {
    if candidate.matches() {
      result .= candidate // error: construction state is unknown inside the loop
      break
    }
  }
}
```

The loop body may run more than once, so the compiler cannot tell whether this
`.=` constructs or replaces. Make the construction point visible, for example
by finding the match in the loop and constructing `result` once after it.

The rule applies to result slots and to members under explicit construction
control:

- At every point in the body, each such place is **empty** or **live**.
- State changes only at visible operations. `.=`, `+++`, a value-bearing
  `return`, a routing entry, and an `opaque-construction` enclosure naming the
  place make it live. An explicit `---` makes it empty again.
- Where control-flow paths join, including the return to the top of a loop,
  places whose states differ become **unknown**.
- Conditions are never evaluated. Even a constant condition such as
  `while true` is treated as possibly true or false.
- At each `.=`, the state must be known. Empty means first construction; live
  means replacement; unknown is an error that asks the programmer to
  restructure the code.
- At normal completion, every result slot and every required member must be
  live.

The analysis is static; Zax does not require per-instance runtime flags.
Compiler debugging modes may add checks without making those checks language
guarantees.

The compiler also diagnoses:

- use before construction;
- constructing one still-live member lifetime more than once through `+++`;
- conflicting call-site and constructor-body construction of one member; and
- normal return with an incomplete instance.

## Construction packets

A construction packet distinguishes positional constructor arguments, named
constructor arguments, and stored-member initializers:

```zax
[{
  positionalExpression,
  parameterName: namedExpression,
  : explicitlyPositionalExpression,
  :,
  parameterWithDefault:,
  .memberName = memberExpression
}]
```

- `expression` supplies an ordinary positional constructor argument.
- `parameterName: expression` supplies a named constructor argument.
- `: expression` makes positional intent explicit.
- empty `:` or `parameterName:` explicitly omits a parameter and requires its
  declared default.
- `: :` or `parameterName: :` explicitly supplies the parameter type's default
  value at that packet position.
- `.memberName = expression` supplies direct call-site initialization for a
  stored member.

The categories are explicit. A named argument never falls back to a stored
member, and a stored-member entry never falls back to a parameter.

For a variant destination, `.alternativeName = expression` instead selects and
directly constructs that named payload:

```zax
choice : MyChoice = [{
  .text = "ready"
}]
```

A variant wrapper packet has zero alternative entries for absent default
construction or exactly one for presence. It accepts no wrapper-level
positional or named constructor arguments because the protected wrapper has no
programmer constructor. A nested typed declaration can supply a packet to the
selected payload. Complete behavior belongs to
[Zax variants](variants.md#default-and-direct-construction).

An unmanaged union has no construction-packet form; it zero-fills and receives
ordinary lens writes. See
[Zax unions](unions.md#forming-an-immutable-or-compile-time-union).

The packet syntax is current conceptual design and should be used in
construction examples. Its formal grammar may be refined if later concrete
pressure requires a change.

`[{}]` is the canonical zero-entry packet:

```zax
explicit : MyType = [{}]
```

For an ordinary type it selects the same zero-input construction as an
initializer-free declaration when available. For `MyType?`, it explicitly
constructs a present wrapper containing a zero-input-constructed `MyType`;
initializer-free optional construction is absent.

`[{ }]` is a confusable-form intent error because its blank body looks like the
programmer opened a nonempty packet but forgot its arguments. Contiguous `[{}]`
acknowledges intentionally zero inputs. A packet is also not independently an
expression value. An already selected concrete call parameter may supply its
destination in argument position; otherwise anonymous typed construction
supplies a destination where an expression hole needs one. Complete call,
optional, and source-structure consequences are defined by
[Zax function invocation](function-invocation.md#construction-packets-as-arguments),
[Zax optional values](optional-values.md#empty-construction-packets), and
[Zax source structure](source-structure.md#optional-layers-and-empty-construction-packets).

### Declaration expressions as inputs

An entry expression may itself declare a temporary:

```zax
[{
  : myValue : String,
  name: temporary : String,
  .member = makeMember()
}]
```

The outer `:` or `name:` categorizes the packet entry. The nested declaration
expression introduces and evaluates the temporary. A bare declaration
expression in a label-capable entry is an intent error; explicit positional
intent or grouping disambiguates it. See
[Zax function invocation](function-invocation.md#argument-declaration-expressions).

### Positional cursor

Argument mapping is tested separately for each constructor candidate:

1. At packet entry, the positional cursor points to the first parameter.
2. A bare or explicitly positional expression binds the current positional
   parameter and advances.
3. `name: expression` binds that named parameter and resets the cursor to the
   parameter following it.
4. An empty positional or named omission maps its parameter and advances or
   resets the cursor in the same way, but evaluates no value during the explicit
   input phase.
5. `.member = expression` does not establish a constructor position and
   invalidates the positional cursor.
6. A later named constructor argument may establish the cursor again.
7. A positional entry while the cursor is invalid is not viable.
8. A positional entry does not skip a defaulted parameter.

```zax
[{
  .legs = 2,
  kind: "grizzly",
  afterKind
}]
```

`afterKind` maps to the parameter after `kind` for each candidate. Different
candidates may map it differently. Ordinary viability and ranking choose a
clear winner; an unresolved tie is an error.

Closely overlapping overload contracts can still create logic mistakes when a
later declaration change makes another candidate a better match. Zax diagnoses
ambiguity but does not prohibit advanced overload sets whose distinctions
require care.

### Number literals in constructor selection

Number literals participate in the same constructor selection after arguments
are mapped. Suppose `MyValue` has constructors accepting `U32` and `I8`:

```zax
myValue : MyValue = 55 // error: both constructors accept 55
myU32Value : MyValue = (: U32 = 55) // selects the U32 constructor
```

The compiler tests only the declared parameter types. Suppose `MyContainer`
accepts `MyInteger`, and `MyInteger` can itself be constructed from an integer.
The compiler does not invent that nested construction:

```zax
myContainer : MyContainer = 55
// error: MyContainer requires MyInteger, not an integer parameter

myContainer : MyContainer = (: MyInteger = 55)
```

The second form explicitly creates the `MyInteger` value. Integer argument
selection and the no-invented-temporary rule are explained by
[Zax integer literals and realization](integer-literals.md#when-a-visible-type-may-complete-an-operand).

### Contextual and explicit constructors

An operator with a visible typed peer may sometimes request the same
construction without the programmer writing the nested declaration:

```zax
myValue := 0 + (: MyInteger = 5)
```

That construction may run user code and has real effects and costs. It is
available only when the constructor and operator both opt in with the
`contextual` completion mode.

Illustrative constructor syntax:

```zax
+++ contextual final : ()(rhs : Integer) = {
}
```

Assume `MyContextualInteger` also provides a contextual `+`. Its constructor
allows the missing left value to be built:

```zax
myValue := 0 + (: MyContextualInteger = 5)
```

For construction, this is equivalent to:

```zax
myValue :=
  (: MyContextualInteger = 0) +
  (: MyContextualInteger = 5)
```

An explicit constructor refuses that inserted construction even when `+`
permits contextual completion:

```zax
myValue := 0 + (: MyExplicitInteger = 5)
// error: MyExplicitInteger requires explicit construction

myValue :=
  (: MyExplicitInteger = 0) +
  (: MyExplicitInteger = 5)
```

The second form supplies both values explicitly and may use the ordinary
operator.

`explicit` requires source to provide the constructed value before the compiler
may select a candidate that needs it, and omission defaults to `explicit`. The
exact final keyword position remains future declaration/source integration.
Operator participation and bounded fallback are defined by
[Zax operators](operators.md#contextual-completion).

Constructors and replacement constructors use the ordinary parameter-default
and omission model. A packet may omit an input only when the tested candidate
declares a default. Complete omission, default evaluation, and fixed-arity
preference are defined by
[Zax function invocation](function-invocation.md#omitted-inputs-and-defaults).

### Evaluation and binding order

Every value-producing packet entry evaluates strictly from left to right. The comma is a
non-overloadable sequencing operation for this purpose. Other expression
precedence rules still apply inside each entry.

For each value-producing entry in source order:

1. evaluate its expression;
2. immediately initialize or bind the selected input slot according to its
   `copy`, `move`, `last`, reference, pointer, or other parameter semantics; and
3. complete the observable effects of that binding before evaluating the next
   entry.

Empty omission entries perform no evaluation in packet order. After all explicit
value-producing entries, including stored-member inputs, complete, declared
defaults for still-unbound constructor parameters evaluate in the selected
constructor prototype's parameter order.

After every input is ready, member construction follows the selected constructor
plan. Packet order does not reorder members.

For a dynamically allocated destination, all required object and detached
control-block storage is obtained before this packet evaluation begins.
`@!` therefore returns a vacant pointer without evaluating packet entries when a
storage request fails. Complete allocation ordering is defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-order-and-failure).

- A copied input captures the source value when that entry is evaluated.
- A reference input binds at evaluation but observes later changes to its
  referent.
- A by-value `deep`, `move`, or `last` input performs its required parameter
  construction during that entry's binding.
- A `move`- or `last`-stanced reference binds directly to caller-owned storage;
  the selected lifecycle body may take resources later during its complete
  call.
- A call-site member initializer supplies the already evaluated input used when
  the construction plan reaches that member.

```zax
source : Integer = 1

value : Example = [{
  .snapshot = source,
  changeSourceToTwo(source)
}]
```

If `snapshot` selects `copy` semantics, its input captures `1` before
`changeSourceToTwo` runs. If it selects reference semantics, the bound reference
later observes `2`.

The compiler may use a conceptual temporary to preserve this behavior and may
elide it only when observable `copy`, `move`, construction, and destruction behavior
does not change.

A temporary bound to a reference input survives through completion of the
selected lifecycle call. Other packet temporaries survive until their values
have been transferred and their specified destruction point is reached.
Complete temporary and full-expression rules remain future function and
lifetime work.

### Multiple-result inputs

A bare multiple-result invocation in a packet may supply consecutive constructor
parameters:

```zax
pair : Pair = [{
  produceTwo()
}]
```

This uses the shared
[result-forwarding model](function-invocation.md#forwarding-results). It does not
pack the results into one structural value and does not initialize stored members
by result label.

### Mixed call-site member initialization

A `.member = expression` entry:

- suppresses that member's ordinary default initialization;
- evaluates its expression in packet order;
- initializes the member when the construction plan reaches it; and
- completes before the enclosing constructor body begins.

This is one construction of the direct member, not default construction followed
by assignment.

An explicit `_.member .= ...` or `_.member.+++()` in the selected constructor
body conflicts because both operations attempt to construct the same member. The compiler diagnoses
that conflict.

Low-level code may eventually use a narrow unsafe lifecycle assertion when
opaque control flow makes the apparent conflict valid. The syntax and formal
guarantees for such assertions remain future safety and analysis-control work.

### Structural construction and transformation

Safe compatible by-value conversion runs applicable destination construction
using a verified compatible source view. `-<` recomposition and `-<>-`
transformation likewise establish one complete destination under ordinary
member and enclosing-lifetime rules.

An `own`-published or data-`via` destination name used by transformation is
only a designator for its physical place. Construction must establish the
complete containing member; it never default-constructs that member and then
assigns through the route silently.

A type with custom copy or lifecycle behavior remains an atomic structural leaf
unless a future type-owned bridge permits flattening through it. A same-storage
compatible reference begins no destination lifetime, so direct destination
construction or destruction through that view is unavailable.

Complete shape, posture, anchor, recomposition, transformation, and reshape
behavior belongs to
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

### Anonymous structural values are distinct

A future anonymous-structure expression would produce one structural value:

```zax
animal : Animal = {{
  value: first,
  kind: "grizzly",
  legs: 2
}}
```

The doubled braces are illustrative future syntax, not current structural-type
design. The semantic distinction is that one anonymous value becomes one
constructor input; it is not a packet containing several arguments and member
initializers.

### Array element construction

The outer `[ ... ]` lists array elements. When an element needs constructor
arguments, put one construction packet at that element's position:

```zax
items : MyItem[3] = [
  [{ "first", 3 }],
  [{ "second", 7 }],
  [{}]
]
```

Each packet receives `MyItem` as its destination type and follows the ordinary
argument mapping, evaluation, default, and lifecycle rules in this document.
The packet itself still has no independent type.

Array source expressions evaluate once from left to right. When every sequence
contribution has an exact count, the complete destination backing request
finishes before the first element construction. An uncounted iterable may grow
incrementally; each backing expansion and any required relocation complete
before construction of the next element.

Elements construct from the lowest index upward and are destroyed from the
highest index downward. If construction panics, ordinary code never receives
the incomplete array or a successfully constructed prefix. Complete element
counts, iterable expansion, relocation, and element access are explained by
[Zax arrays and slices](arrays-and-slices.md#construction-and-destruction).

Extra reserved capacity contains no elements, so nothing needs to be
constructed for it.

## Declared and generated lifecycle operations

For the lifecycle operations reviewed here, Zax distinguishes five declaration
states:

| Form | Meaning |
| --- | --- |
| Declaration with a body | Use the programmer-defined implementation |
| Bodyless declaration | Claim an unavailable shape that blocks weaker selection until later completion supplies an implementation |
| `= default` | Require the compiler to supply the language-defined default implementation |
| `= existing` | Explicitly satisfy this shape using a compatible existing operation |
| `= forbidden` | Permanently prohibit this operation shape |

`forbidden` is a hard prohibition. Generation or compatibility fallback cannot
restore the operation.

### Bodyless declarations

A bodyless declaration claims its signature and suppresses automatic generation
for that shape. It participates in match-quality comparison while remaining
unavailable for invocation.

- If it is the best match, selection fails because the type does not currently
  support the operation. The compiler does not fall through to a weaker
  candidate.
- If a latent generated candidate would match better, the compiler requires an
  explicit declaration for that better shape rather than generating it
  silently.
- Later completion may supply the missing implementation.

A bodyless declaration and an abstract composition role answer different
questions. A bodyless lifecycle declaration reserves an unavailable operation
on this same type until later completion provides it. An `abstract` role asks an
immediate container to provide a declaration and requires that declaration to
name the role with `fulfill`. One form never turns into the other implicitly.
See
[`abstract` roles and `fulfill`](composition.md#abstract-roles-and-explicit-fulfillment).

Composition also defines `= via member.operation` adapters and
`= forbidden family` exposure fences. Neither adds another lifecycle
declaration state to this table.

### `default`, `existing`, and `forbidden`

`= default` selects the language-defined default implementation for that exact
declaration shape. The compiler must supply it or diagnose that no default
exists:

```zax
+++ final : ()() = default
```

An arbitrary function does not acquire invented behavior merely because the
syntax parses:

```zax
myFunc : ()() = default // error: no language-defined default implementation
```

`= existing` deliberately reuses a compatible operation:

```zax
+++ final : ()(value : Value copy) = {
  // Programmer-defined copy behavior.
}

+++ final : ()(value : Value move) = existing
```

Callable compatibility alone does not establish lifecycle-contract
compatibility. `existing` makes reuse explicit when `copy`, `move`, source-state, or
cost behavior might otherwise differ.

`= forbidden` prohibits the exact shape:

```zax
+++ replacement final :
  ()(source : Source) = forbidden
```

### Demand-driven generation

The programmer declares only operations intended for use. Zax does not require
an exhaustive list of theoretical variations.

When an actual use could choose between a compatible existing declaration and a
newly generated specialization with meaningfully different behavior, the
compiler does not guess. The programmer resolves the demanded shape by:

- providing a body;
- writing `= existing`;
- writing `= default`;
- writing `= forbidden`; or
- leaving a bodyless requirement for later completion.

No error is required for an unused latent ambiguity.

A generated empty constructor exists only when every required member has a
viable default or declared initialization path. Declaring the default-construction
shape suppresses generation of that shape.

For a containing type, a generated operation selects member operations that
satisfy the generated lifecycle contract. It does not select any callable member
operation merely because one exists.

Generated `copy` is the conservative transfer baseline. Zax does not generally
generate `deep`, `move`, or `last` operations:

- `deep` requires an exact `deep` declaration;
- `move` falls back to `copy` when no `move` declaration exists;
- `last` falls back to `move` and then `copy`;
- and a generated containing `copy` selects the exact member `copy` operations
  required by its contract.

Complete stance meaning and fallback are defined by
[Zax transfer stances](transfer-stances.md#fallback).

Reflection must eventually distinguish declared, generated, explicitly
defaulted, delegated-to-existing, bodyless, and forbidden operations.

### Generated copy construction and assignment

Generated `copy` is one compiler-owned schema exposed as exact,
qualifier-complete declarations. It is not a qualifier-erased prototype.

Construction creates a new value lifetime, so both mutable and immutable values
may have generated `copy` constructors. For a mutable value shape, the compiler
conceptually supplies exact source-place variants:

```zax
+++ final : ()(
  : MyValue mutable readonly final & copy
) = default

+++ final : ()(
  : MyValue mutable readonly varying & copy
) = default
```

The source parameter is compiler-anonymous. Construction has no receiver stance
because no destination lifetime exists yet.

A corresponding immutable value shape may receive immutable source constructor
variants. Ordinarily `mutable` and `immutable` qualify the same representation. Future
mutability-indexed type-family design may instead permit independently defined
shapes; each such concrete shape still receives only its appropriate
constructors.

Canonical same-type `=` treats the destination as a complete value. Its receiver
place must therefore be `varying`: that place permits the complete value to be
assigned as a unit. For a mutable varying receiver, the compiler conceptually
supplies:

```zax
operator binary '=' final : (
  : MyValue mutable readonly varying & copy
)(
  : MyValue mutable readonly final & copy
) mutable writable varying = default

operator binary '=' final : (
  : MyValue mutable readonly varying & copy
)(
  : MyValue mutable readonly varying & copy
) mutable writable varying = default
```

The result and source slots are compiler-anonymous. The language-defined body
returns readonly `copy` access to the assignment receiver `_`, permitting
right-associated assignment:

```zax
first = second = third
```

The `final`-source and `varying`-source forms are different declarations.

A `final` receiver promises that its place continues to hold the same value
lifetime. It may still mutate the contents of a mutable value through eligible
member functions, member assignments, or a domain-specific in-lifetime `=`, but
the current generated whole-value assignment family remains unavailable:

```zax
stable final : MyValue mutable final

stable.member = replacement // may mutate the current value
stable .= anotherValue      // error: complete reconstruction needs a varying place
```

A domain-specific `=` may explicitly accept a `final` receiver when it defines
coherent in-lifetime behavior rather than replacing the complete value.

An immutable value cannot use an ordinary `=` body to mutate its contents.
When its place is varying and the path has writable replacement authority,
protected `.=` can instead end the complete resident lifetime and establish a
successor. Mutable values may also choose `.=` when explicit lifetime renewal is
intended.

One concrete value shape is `mutable` or `immutable`, never both. There is no
runtime branch over mutability or place stance inside one generated declaration.
An independently defined immutable shape receives constructors but no ordinary
mutating assignment.

Generation is suppressed or replaced only by the exact generated shape. A
nearly matching programmer declaration may coexist with the generated
declaration and produce demand-time ambiguity. Diagnostics should show the exact
generated signature and the mismatching layer.

## Reconstructive replacement

### Why replacement exists

Protected `.=` is the whole-value transition that ends one complete resident
lifetime and establishes a successor in the same place:

```zax
message varying :
  Message immutable writable varying = makeMessage("first")

message = makeMessage("second")
// Ordinary `=` has no lifecycle authority and cannot mutate Message.

message .= makeMessage("second")
// The first immutable lifetime ends; a successor begins.
```

The distinction is visible for mutable values too:

```zax
mutableMessage varying :
  Message mutable writable varying = makeMessage("first")

mutableMessage = makeMessage("assigned")
// Ordinary in-lifetime assignment when a viable `=` exists.

mutableMessage .= makeMessage("reconstructed")
// Explicit complete lifetime renewal.
```

Complete reconstruction requires:

- type-side `varying`, because the place receives a successor lifetime;
- declaration-side `varying`, because this path initiates replacement;
- writable access;
- a suitable same-storage representation;
- a viable replacement hook or ordinary fallback; and
- no exact prohibition.

It does not require the old value to be mutable. Construction authority
establishes the successor's mutable or immutable state.

These requirements apply only to a live place. On an empty result slot or an
empty explicitly controlled member, `.=` performs first construction instead
and needs no replacement permission; see
[Construct into an existing place with dot-equals](#construct-into-an-existing-place-with-dot-equals).

Type-side `final`, declaration-side `final`, and readonly access independently
block `.=`:

```zax
stable final :
  Message immutable writable final = makeMessage("stable")

reader :
  Message immutable readonly varying & = message

stable .= makeMessage("blocked") // error: final place
reader .= makeMessage("blocked") // error: readonly path
```

A restricted same-place path observes that transition without being able to
initiate it:

```zax
observer final :
  Message immutable readonly varying & = message

restricted final :
  Message immutable writable varying & = message

restricted .= makeMessage("third")
// error: this declaration lacks replacement permission

message .= makeMessage("third") // legal through the varying declaration
display(observer)              // "third"
```

`restricted` remains writable for operations that do not require whole-value
replacement, and its type use still records truthfully that another path may
replace the referent. Type-side capability versus declaration-side permission is
owned by
[Zax qualifiers](qualifiers.md#type-side-truth-versus-declaration-side-permission).

The compiler owns the protected reconstructive `.=` lifecycle skeleton. It
cannot be overloaded. User-defined code may provide a replacement constructor
hook but does not replace the skeleton with an ordinary operator body.

An ordinary `operator binary '='` retains the receiver's qualifications and has
no transitional construction authority:

```zax
operator binary '=' final : ()(
  newValue : Message
) immutable writable varying = {
  _.text = newValue.text // error: `_` is immutable
  audit(newValue)        // legal external effect
}
```

This is why the customization point is `+++ replacement`, not an overloaded
`=` or `.=` body.

### Replacement inputs and packets

One direct source or a positional/named construction packet can supply the
successor:

```zax
value .= source

value .= [{
  firstInput,
  mode: selectedMode
}]
```

The compiler evaluates and binds every input before the old lifetime ends. It
then selects one matching `+++ replacement` or applies the ordinary fallback
with the same inputs.

Direct call-site `.member = ...` entries are unavailable for complete
replacement packets. Such entries would compete with the replacement hook's
authority to retain, renew, reconstruct, or disposition old members. A
type-owned replacement hook receives constructor parameters and owns the member
transition itself.

### Generated fallback

When no selected custom replacement constructor handles the transition, the
generated fallback:

1. invokes the old instance's ordinary `---`;
2. retains the already allocated outer storage;
3. invokes the selected ordinary `+++`; and
4. performs the ordinary new member-initialization process.

The fallback assumes that no old resource is intentionally retained through the
transition.

### Custom replacement

A custom replacement constructor uses contextual `+++ replacement`:

```zax
Registration :: type {
  id : RegistrationId
  settings : Settings

  +++ final : ()(settings : Settings) = {
    _.id .= registry.register(settings)
    _.settings .= settings
  }

  --- final : ()() = {
    registry.unregister(_.id)
  }

  +++ replacement final : ()(
    settings : Settings
  ) = {
    registry.reconfigure(_.id, settings)
    _.settings = settings
  }
}

registration varying :
  Registration immutable writable varying = makeRegistration()

registration .= [{ nextSettings }]
// Selects +++ replacement and can retain the registration id.
```

When selected, the replacement constructor:

- suppresses the enclosing ordinary `---`;
- suppresses the enclosing ordinary `+++`;
- receives the prior representation and resources through `_`;
- has transitional construction authority;
- may retain, assign, move, copy, destroy, reconstruct, or otherwise recycle
  old resources while renewing every member resident instance;
- uses the existing outer allocation; and
- must establish a complete valid replacement instance on every normal return.

The example retains the `id` resource and representation. Running the ordinary
destructor first would unregister it and defeat recycling. Complete replacement
nevertheless renews `id`'s member resident instance along with every other
member. Resource reuse is not member-lifetime retention, and leaving bits at an
address after ending a member lifetime does not preserve that old instance.

For a member carried forward in place, renewal is a language-level lifetime
boundary controlled by the replacement constructor:

- the old member resident instance ends as part of the enclosing replacement;
- no ordinary member `---` or `+++` hook is generated around the carried
  representation;
- the replacement constructor transfers that representation and its resources
  into the successor member instance; and
- normal return publishes the successor only after the complete replacement is
  valid.

The retained resource therefore receives one continuing owner rather than a
destructor followed by reconstruction. References to the old member still cross
a real resident-instance boundary even though no member hook ran.

Members start live inside a replacement constructor, because they still hold
the previous representation. `_.member .= value` there replaces that member. If
the body explicitly ends a member with `_.member.---()`, the member is empty,
and the next `_.member .= value` constructs it. Every member must be live and
valid when the replacement constructor returns.

The compiler tracks member transitions. Normal return requires one live valid
value for every required member and exactly one disposition for every resource
the transition ceased to retain. A carried resource counts as transferred into
the successor member, not left undispositioned.

### Replacement constructors have no results

A replacement constructor declares no results, exactly like an ordinary
constructor:

```zax
BufferOwner :: type {
  buffer : Buffer
  format : Format

  +++ replacement final : ()(nextFormat : Format) = {
    // Establish the complete replacement instance, reusing the buffer's
    // capacity when it can.
  }
}
```

Keeping both lifecycle hooks resultless lets every `.=` produce the same value:
access to the place it constructed, whether that `.=` constructed an empty
place or replaced a live one. Code that needs to know something about a
replacement asks before replacing:

```zax
willReuse := owner.buffer.canReuseFor(newFormat)
owner .= [{ newFormat }]
```

Every normal return must complete the replacement instance. Constructors,
replacement constructors, and destructors cannot produce exceptional outcomes
either. A lifecycle body calling an exception-producing function must handle
every outcome locally and still complete its lifecycle obligation; it cannot
forward through `except`.

### Candidate selection

Replacement-constructor overloads use ordinary parameter mapping, viability,
qualification, and preference for the direct source or packet inputs. An
unresolved best replacement hook is an error. Ordinary domain-specific `=`
declarations do not compete because complete reconstruction uses the distinct
protected `.=` form.

Direct compound and mixfix operations remain separate:

```zax
destination += rhs
destination[index] = rhs
```

The compound form invokes its own selected mutation candidate. The indexed form
may select one direct mixfix whose receiver, index, and RHS evaluate once. Neither
is automatically rewritten through a value operation followed by `=`.

A user mixfix that consumes a written `=` component does not acquire the
compiler-owned reconstructive lifecycle skeleton. If its body mutates a current
value, ordinary mutable/writable authority applies. Protected `.=` is not a
user-consumable mixfix component.

See [Zax mixfix operators](mixfix-operators.md#qualifications-and-lifecycle) for
tree matching and the
[integer operator catalog](integer-operator-catalog.md#compound-arithmetic) for
protected integer compounds.

When an actual use exposes an existing-versus-generated choice, the programmer
resolves the demanded shape with a body, `existing`, `default`, `forbidden`, or a
bodyless declaration.

Construction and replacement may be controlled independently:

```zax
+++ final : ()(source : Source) = default

+++ replacement final :
  ()(source : Source) = forbidden
```

This type permits construction from `Source` while prohibiting replacement from
`Source`.

A separate domain-specific operator may return any result allowed by ordinary
operator rules. That is distinct from `.=`, which always produces access to
the place it constructed.

### Self-aliasing and interior aliases

Evaluating a right-hand expression before replacement begins does not guarantee
independence. A reference result may point into the destination:

```zax
Document :: type {
  text : String

  +++ replacement final : ()(
    source : String readonly &
  ) = {
    _.text = source
  }

  stringViewFrom final :
    (result : String readonly &)() = {
    return _.text
  }
}

document varying : Document immutable writable varying
document .= document.stringViewFrom()
```

`source` may alias `_.text`. Delegating to `String`'s selected `=` lets `String`
own exact self-alias handling, but it does not remove the general pressure.

The same issue appears in:

```zax
value .= value
value .= viewOf(value)
value .= makeReplacementUsing(value)
```

Safe cases may include:

- a by-value snapshot established before transition;
- an operation designed to handle exact self-aliasing; or
- a lifetime policy that proves nonconflicting access.

The compiler diagnoses conflicts required by the applicable language contract.
A future narrow unsafe permission may allow a use whose safety the compiler
cannot prove. The permission belongs at the risky operation, not on every `&`
declaration.

Complete reference origin, fixed place binding, and the unsafe successor-member
boundary are defined by
[lifetimes and references](lifetimes-and-references.md#direct-member-references-cross-a-renewal-boundary).

### Raw pointers

Same-storage replacement does not categorically invalidate every raw pointer.

- A pointer into retained storage may continue to name the same location.
- A pointer to a value whose lifetime ended no longer identifies that old live
  value, even when another value later occupies the address.
- Reusing an address does not by itself decide whether the intended pointee
  survives.

Raw pointers carry no ownership or intrinsic lifetime guarantee. A use is safe
when analysis proves its required origin and lifetime facts; otherwise it needs
narrow unsafe responsibility. Managed ownership, including interior pointers to
direct members, is defined by
[pointers and arenas](pointers-and-arenas.md).

### Representation boundary

Same-storage replacement requires the existing place to satisfy the selected
replacement representation's size, alignment, and layout.

If a future type-family design gives mutable and immutable variants different
representations, sharing one family name does not make cross-variant
same-storage replacement viable. A different representation may require
separately suitable storage and a construction, `copy`, or consuming
transformation.

An unmanaged [union](unions.md) has no active-member transition. Its complete
same-type operation copies every backing bit and invokes no lens lifecycle hook.
A managed [variant](variants.md) instead owns zero or one conditional payload
path and dispatches wrapper operations through the active alternative.

### Optional same-type assignment and complete-wrapper replacement

Same-type optional `=` assigns absence/presence state while retaining one
mutable wrapper lifetime. Same-type optional `.=` instead reconstructs the
complete wrapper lifetime:

```zax
destinationOptional : MyType?
sourceOptional : MyType?

// ...

destinationOptional .= sourceOptional
```

Complete `.=` requires a type-side varying wrapper place, declaration-side replacement
permission, writable access, and a compatible optional source transfer. The old
wrapper and any present payload end; a new absent or present wrapper is directly
constructed from the source state.

Ordinary `=` requires a mutable wrapper through writable access. Its wrapper
place may be final or varying because the current wrapper lifetime continues,
although its old payload path may end.

Resolved source depth distinguishes complete-wrapper reconstruction from
wrapper-owned contained reconstruction, which retains one mutable wrapper
lifetime while ending and constructing boxed lifetimes:

```zax
destination .= sourceValue
```

It is also distinct from an operation selected after proven boxed access:

```zax
if ?destination
  destination. = sourceValue
```

Complete-wrapper `.=` requires a varying wrapper place, declaration-side
replacement permission, and writable access. Payload-source `.=` instead
requires a mutable wrapper through writable access; that wrapper place may be
final because its own lifetime continues.

The optional family does not turn ordinary value-shaped `T? = T` source into
construction. Complete state, qualification, intent, and transfer behavior is
owned by
[Zax optional values](optional-values.md#construction-wrapper-replacement-and-boxed-assignment).

## Wrapper-owned contained reconstruction with dot-equals

Optional and variant wrappers specialize protected `.=` to keep one wrapper
lifetime while ending and constructing their contained state:

```zax
optional .= value
optional .= [{ constructorArguments }]

variant.text .= value
variant.text .= [{ constructorArguments }]
```

The operation:

1. evaluates the wrapper and contained target designator once;
2. evaluates and binds every direct source or packet input;
3. proves those inputs survive the transition;
4. destroys the old payload when present;
5. constructs the requested payload; and
6. publishes presence or the active alternative only after construction
   completes.

The wrapper must be mutable and the current path writable. Its place may be
final or varying because this operation does not replace the complete wrapper
lifetime. Type-side `final` at the wrapper layer therefore does not select or
permit `+++ replacement`; no complete wrapper replacement occurs.

Optional and named-variant payload forms return access to the newly constructed
payload. A packet targeting the complete variant wrapper returns wrapper access,
because a zero-entry packet constructs absence and has no payload:

```zax
variant .= [{}]
variant .= [{ .text = "ready" }]
```

The wrapper-packet form accepts zero or one named alternative entry. A bare
variant `variant .= value` cannot infer an alternative from payload type.

The right side is secured before the old payload ends. A source reference into
that old payload remains an alias hazard even when evaluated first:

```zax
if ?optional
  optional .= optional. // error when construction needs the ended payload
```

The selected construction must make an independent snapshot, consume the alias
before renewal, deliberately support the relationship, or use the applicable
`replacement-alias` unsafe responsibility.

A panic leaves the same contained-reconstruction operation blocked. It does not
publish a partial payload, restore the old payload, or continue ordinary
execution through absence.

Wrapper-owned `.=` performs fresh ordinary payload construction. It does not
select the old payload type's `+++ replacement`. To replace an already active
payload as a complete varying value, first access or bind that payload and then
apply complete `.=` reconstruction:

```zax
if ?optional
  optional. .= [{ replacementInput }]

switch variant {
  case replaceable
    replaceable .= [{ replacementInput }]
}
```

Those target payload places must themselves be varying and writable. A final or
readonly payload path cannot select `+++ replacement`.

`.=` remains protected and cannot be overloaded. Complete optional and variant
behavior belongs to [optional values](optional-values.md) and
[variants](variants.md).

## Destruction

### Destructor declarations and sequence

A concrete type has at most one destructor:

```zax
--- final : ()() = {
  // Dismantle the current instance.
}
```

A destructor accepts no ordinary input arguments and returns no results.

Ordinary destruction:

1. enters terminal destruction authority;
2. runs the enclosing `---` body while every not-yet-destroyed member remains
   live;
3. automatically destroys remaining members after the body; and
4. ends the enclosing lifetime.

Automatic member destruction follows reverse declaration/default-construction
order. It does not reconstruct the arbitrary runtime order chosen by explicit
constructor-body `+++` calls.

An explicit `_.member.---()` call may run in any destructor-body control-flow
order and suppresses the ordinary generated destructor call for that member
lifetime. The compiler tracks which member lifetimes remain live at every normal
destructor exit.

Destruction receives terminal mutable and writable authority. It may dismantle
resources but cannot create an access path valid beyond the enclosing lifetime.

The ordinary pre-unary `dispose` phrase used by
[Zax `using`](using.md#structural-disposal) is not
destruction. It operates on a complete still-live receiver under its declared
qualifications. The receiver remains alive for later destruction after
`dispose` completes.

### Reconstruction during destruction

A destructor may reconstruct a member:

```zax
--- final : ()() = {
  _.member.---()
  _.member.+++()
  use(_.member)
  _.member.---()
}
```

The two member destructor calls end two different lifetimes. Lifecycle analysis
should distinguish them rather than report double destruction of one lifetime.

Advanced terminal code may need a narrow future unsafe permission when required
analysis cannot prove its lifecycle behavior. Generated destruction suppression
depends on tracked member state at normal exit, not merely on the presence of a
`---` token.

The exact severity of optional diagnostics and any runtime debugging checks
remain future safety and tooling work.

## Storage added by partials

A type that writes `seal open storage` may receive stored members from
[partials](partials.md). The type's constructors cannot name those members, so
each partial establishes and ends its own storage:

```zax
MyCatalog :: type seal open storage {
  entries : Integer = 0
}

MyTagging :: partial MyCatalog {
  tag : String

  +++ final : ()() = {
    _.tag .= "none"
  }
}
```

A partial may declare only a no-argument `+++`, and the compiler runs it
automatically. A partial's `---` also runs automatically. The sequence for one
`MyCatalog` is:

1. `entries` and the type's other automatic members are constructed;
2. each partial's storage is constructed, in partial definition order;
3. the selected `MyCatalog` constructor body runs.

Destruction mirrors it: the `---` body runs, then each partial's storage is
destroyed in reverse, then the type's members.

Generated copy and assignment include partial storage. A hand-written copy
constructor or `=` cannot name it, so partial storage takes part only when the
body calls a hook that partials fulfill.

During every replacement of the type, including the
[generated fallback](#generated-fallback), each partial's storage runs the
partial's no-argument `+++ replacement` if it declares one. Like any replacement
constructor, it starts with that storage still live, so it can keep or recycle
the previous state. Otherwise the partial's storage receives `---` and then
`+++`, which resets it.

Hooks, inclusion rules, and ordering guidance are defined by
[Zax partials](partials.md#adding-storage).

## Scope-exit destruction and flow transfers

Local, body, and flow-header lifetimes end automatically when their scope exits,
whether that exit is a normal completion or an abrupt transfer. Destruction runs
in reverse construction order.

On normal body completion:

1. body-local bindings are destroyed;
2. the construct runs its post operation when it requires one, while header
   bindings remain alive;
3. the construct tests, re-enters, or exits; and
4. header bindings are destroyed when the complete construct exits.

An abrupt transfer such as `break`, `continue`, `next`, `goto`, or `return`
destroys every body or nested scope it leaves, in reverse construction order,
before control arrives at the target:

```zax
while i := 0 ;; i < 100 ;; ++i {
  resource := acquire()
  if done(resource)
    break // resource is destroyed, then i, as control leaves the loop
}
```

Which scopes a transfer crosses, whether the post operation runs, and how targets
are selected are owned by
[core flow control](core-flow-control.md#unwinding-destruction-and-completion).
This document owns the automatic local and header lifetime order that results.
Construction and result completeness must still hold on every normal path a
branch, loop, or transfer produces.

For construction and destruction, the important consequences are:

- exited body-local lifetimes end before the transfer arrives;
- a re-entered target's already-active header lifetimes remain alive;
- every `next`, `continue`, or `goto` re-entry starts a fresh body scope at its
  beginning after the previous body scope is completely destroyed and cannot
  jump into a partially constructed scope; and
- every incoming, repeated, and exiting path must preserve required construction
  and destruction state.

Exceptional function completion applies the same lifecycle foundation while
selecting another declared result shape. Before the selected outcome maps:

- body locals and crossed resource scopes complete ordinary exit cleanup;
- the callee destroys every live body-constructed provisional result outside
  the selected outcome; and
- caller-side invocation machinery destroys every live
  prototype-preinitialized ordinary result outside that outcome.

An exceptional result has no prototype initializer and cannot be constructed
early. Producer `except` or forwarding constructs it exactly when selecting
that outcome. The exact cross-boundary order, handler lifetime, and
conditional-elision consequences are defined by
[Zax exceptional result flow](except.md#exact-exceptional-completion-order).

A `using` body adds a disposal phase between its body-local and owned-header
destruction:

```zax
using (
  first := acquireFirst(),
  second := acquireSecond(first)
) {
  temporary := prepare(first, second)
  use(temporary)
}
```

The lifecycle order is:

1. destroy body-local `temporary`;
2. invoke `dispose second`, then `dispose first`, when those operations are
   compatible;
3. destroy `using`-owned `second`, then `first`.

No enrolled receiver is destroyed between disposal calls. Borrowed entries are
not destroyed by `using`. When result routing introduces owned entries, their
destruction follows reverse caller-visible entry order even when
prototype-defined pre-body construction or implementation-defined body and
return construction followed another order. Elision may unify a source slot
with one of those destinations, giving the unified value the destination's
lifetime and destruction position. Complete enrollment, disposal, ordering,
and bypass behavior are owned by [Zax `using`](using.md#ordering); complete
result-elision behavior is owned by
[function invocation](function-invocation.md#result-slots-destinations-and-elision).

Explicit lifecycle calls such as `_.member.+++()` and `_.member.---()`, and
allocation operations using `@`, remain ordinary ordered body operations. A
transfer may bypass a later operation, while a backedge may reach an earlier
operation again. The transfer neither supplies a skipped construction or
destruction nor makes a repeated lifecycle transition or allocation valid.

## Conditionally live storage and access proof

### Optional construction must establish the contained value

Constructing a present `T?` succeeds only when its selected construction can
establish a valid `T`:

```zax
myValue : MyType
myOptional := (: MyType? = myValue)
```

An optional may forward an initializer into a contextual contained constructor,
but failure remains an error:

```zax
myOptional := (: U8? = 355) // error: 355 does not fit U8
```

It does not become absent, narrow the value, or select default optional
construction after present construction fails. Absence occurs only when the
selected operation explicitly requests or produces it.

Type-default optional construction is absent, while `[{}]` explicitly requests
present zero-input boxed construction. A packet on an existing mutable/writable
wrapper evaluates and binds its inputs, ends any old boxed lifetime, constructs
the fresh value, and marks presence only after completion.

Reset ends the boxed lifetime and leaves the same wrapper absent. Direct
destruction through boxed access is rejected because it could not update wrapper
presence:

```zax
optional.---() // error
```

Complete optional operations, nesting, qualification, and transfer are defined
by [Zax optional values](optional-values.md). The number-literal application is
explained by [Zax integer literals and realization](integer-literals.md#optional-construction-still-has-to-build-a-value).

### Access proof

Some storage may or may not hold a live value on a given path. Ordinary direct
access requires proof that a live value exists unless the feature defines an
explicit checked access:

> Access through storage whose value lifetime may not be active requires proof
> or a feature-defined checked operation before the value is used.

The concern is whether a value lifetime is active, not how a particular feature
spells its presence test. It covers conditionally live versus merely
unconstructed storage, use before construction, and proof that a value lifetime
is active before access.

Optional stored-value dereference is one application. Postfix optional
dereference produces a reference to the stored value and requires static proof
that the optional holds a live value on the dereferencing path. Absent that
proof, the dereference is a semantic error rather than a runtime hazard:

```zax
if ?optionalValue
  use(optionalValue.) // proven live on this path
```

Test presence first and dereference inside the proven body. A compound proof such
as `?optionalValue && ?optionalValue.` proves nothing unless the right expression
is unambiguously exactly `Boolean`, because only then does the protected
short-circuit operation skip the dereference; see
[operators](operators.md#optional-and-variant-presence).

The proof need not be immediately adjacent. Construction, earlier control flow, a
preceding presence test, or another recognized presence contract may establish
it. An arbitrary user-defined Boolean-returning `?` does not by itself prove
initialization; the analyzer needs a recognized presence contract.

Wrapper qualifications do not replace the boxed qualifications after access.
Once proof permits `optional.`, the resulting path carries the boxed
qualifications. Any operation ending that exact boxed lifetime invalidates the
proof and every reference tied to it; see
[Zax optional values](optional-values.md#presence-proof-and-postfix-access).

Named variant access is the checked application. A proved active name removes
the check, a proved inactive name is an error, and unresolved selection performs
the registered `inactive-variant-access` check and panics on mismatch. A variant
switch route establishes proof and binding without another check. Complete
behavior is defined by
[Zax variants](variants.md#presence-and-named-access-answer-different-questions).

The presence operation `?value` itself is owned by [operators](operators.md), and
condition placement is owned by [core flow control](core-flow-control.md). This
document owns only the programmer-visible lifetime obligation: access proves a
live value. Detailed proof algorithms, assertion syntax, contract provenance, and
lint separation remain future analysis work, and validity follows the selected
static-analysis contract rather than one compiler's current cleverness.

## Manual and delayed construction

Delayed construction exists only where a place starts empty: result slots and
members under explicit construction control. A local declaration always
constructs its value when it is declared.

### Construction by an opaque operation

Sometimes assembly, foreign code, or another operation the compiler cannot see
is what establishes a member. The constructor states that at the operation:

```zax
MyType :: type {
  s : S
  t : T

  +++ final : ()() = {
    _.t .= [{}]
    unsafe<opaque-construction>{ lowLevelAssemblyCall(_.s) }
  }
}
```

The enclosure asserts that `lowLevelAssemblyCall` constructs `s`. It has three
effects:

- `s` is under explicit construction control, so it receives no automatic
  initialization first;
- after the enclosure, `s` is live for the
  [first-construction rule](#how-the-compiler-decides-first-construction), so a
  later `_.s .= ...` is replacement; and
- the programmer takes responsibility for `s` holding a valid value that its
  later uses and its destructor accept.

Without the enclosure, passing the empty `_.s` to a call is use before
construction. The compiler cannot distinguish a call that establishes a value
from one that reads it, so the claim must be written where it is made. An
enclosure inside a condition leaves `s` in an unknown state, which is an error.

The enclosure must identify exactly which places it establishes; a call that
receives two empty places must not silently establish both. The source syntax
for naming those places is future analysis-control work, as is the final
spelling of the category.

A result slot follows the same rule. When storage outside a type must be
filled this way, wrap it in a type whose constructor makes the assertion, as
shown in
[Zax declarations and bindings](declarations-and-bindings.md#low-level-initialization-belongs-in-a-constructor).

## Incomplete current instances and unsafe controls

A helper may receive the current instance while construction is incomplete:

```zax
+++ final : ()() = {
  _.first.+++()
  initializeRemaining(_)
}
```

The compiler may permit the call when it proves that the helper uses only live
members and completes a valid state. Otherwise a future narrow unsafe
partial-instance permission is required.

The current instance retains its actual immutable qualification while
construction authority permits establishment of its state. An
immutable-compatible helper is preferred. Construction authority may permit a
mutable-compatible helper when no immutable form is viable, subject to the same
partial-state analysis.

Publishing an incomplete current instance is a stronger operation:

```zax
+++ final : ()() = {
  registerGlobally(_)
}
```

Publication may retain a reference, notify subscribers, or reenter through
another path. Permission for bounded helper access does not imply permission for
escape.

Construction performed by an opaque operation uses `opaque-construction`, as
described above. The language needs distinct future unsafe controls for:

- manual member destruction hidden from analysis;
- destruction that analysis cannot prove occurs on every path;
- apparently overlapping destruction calls known to be mutually exclusive;
- bounded partial-instance access;
- partial-instance escape or publication;
- unresolved replacement aliasing; and
- terminal member reconstruction.

Construction has no such override. When the compiler cannot tell whether a
place has been constructed, the code must be restructured so that it can.

The final source syntax, analysis provenance, and contract-version behavior
remain future analysis-control work. The general distinction among proof,
unsafe assertion, unsafe permission, and no valid interpretation is defined by
[safety and analysis](safety-and-analysis.md). No broad unsafe marker can make a
known-ended lifetime valid again.

## Panic and allocation boundaries

An unresolved panic is fatal graceful crashing. A narrowly applicable panic
helper may repair the condition while the operation that encountered it remains
blocked. That same operation then completes as though the failure had not
occurred. Otherwise the process ends.

A panic does not skip the failed operation, supply a substitute value, unwind
completed members, or let ordinary execution continue with partial construction,
replacement, or destruction state.

Automatic allocation occurs before construction of the allocated value.
The selected allocation initializer decides how exhaustion appears:

- `@` prevents normal construction completion and enters panic;
- `@!` constructs a vacant pointer member, so the
  enclosing instance may still complete; or
- a future disabled allocation-failure panic category may omit the `@` check
  under a programmer guarantee, with undefined behavior if failure occurs.

The last case is a general category-specific panic control, not an unchecked
allocation form. Other enabled panic categories remain active.

Complete behavior is defined by
[pointers and arenas](pointers-and-arenas.md#allocation-failure).

Constructors, replacement constructors, and destructors are synchronous.
Suspending lifecycle operations, cancellation, and concurrent replacement remain
future async, lifetime, and concurrency work.

## Identity admission boundary

By-value identity admission requests a new value. It requires an applicable
`copy`, `move`, consuming/`last` transfer, direct construction, or another declared
way to establish the identity's underlying stored value.

Identity syntax does not manufacture copyability. When no applicable
construction or transfer exists, by-value admission is unavailable.

A same-storage identity reference would instead view existing storage and create
no independent value lifetime. Its representation-cast, qualification, alias,
lifetime, and destruction rules remain future identity and casting work.
The compiler does not silently substitute such a view for a requested by-value
result.

General identity behavior is defined by
[Zax identity types](identity-types.md#construction-and-transfer).

## Costs

Programmers must be able to discover:

- automatic versus explicit member lifecycle operations, including the two
  operations performed when ordinary `=` targets an automatically constructed
  member;
- temporaries retained while a construction packet evaluates;
- declared constructor defaults evaluated after explicit packet inputs;
- `copy`, `move`, `last`, and reference binding performed for packet entries;
- right-side capture, old-payload destruction, and new-payload construction
  performed by contained `.=` reconstruction;
- generated fallback replacement as `---` followed by `+++`;
- resources retained or reconstructed by custom replacement;
- copies or snapshots required to avoid alias hazards;
- scope-exit destruction of local and header bindings on normal and abrupt exits;
- destruction of live provisional results excluded by a selected exceptional
  outcome;
- body-local destruction followed by complete reverse disposal and then reverse
  owned-entry destruction for `using`;
- post operations run on `next` and normal completion but skipped on `break`,
  `continue`, and `return`;
- allocator retention and deallocation behavior;
- static-analysis and unsafe-override boundaries; and
- synchronization or async machinery when future facilities use them.

## Diagnostics

Diagnostics should distinguish:

- no viable ordinary or replacement constructor;
- missing, duplicate, unknown, or ambiguously mapped packet entries;
- a direct `.member = ...` entry in a complete replacement packet, where the
  replacement hook owns member transition;
- a positional entry with no current positional cursor;
- omission of a parameter that has no declared default;
- label-versus-declaration intent that is unclear;
- equal viable overloads;
- unresolved existing-versus-generated behavior at an actual use;
- use of a `forbidden` operation;
- a member that cannot initialize automatically;
- ordinary `=` on a member whose type cannot be default-constructed, where
  `.=` would construct it;
- conflicting call-site and constructor-body member construction;
- a `.=` whose target may be empty or live because paths disagree or a loop
  reaches it;
- a second `.=` on a member through a `final` place or readonly path;
- payload `.=` on an optional or variant member whose wrapper is not
  constructed yet;
- an empty member passed to an operation without construction or an
  `opaque-construction` assertion;
- a replacement constructor that declares results;
- use before construction or after destruction where required analysis proves
  it;
- an access through conditionally live storage, such as an optional stored-value
  dereference, without proof that a live value exists on that path;
- missing or duplicate lifecycle transitions on normal paths;
- normal completion with an incomplete instance or result;
- exceptional completion with a live nonselected provisional result that its
  current owner did not destroy;
- an exceptional result initialized or manually constructed before its
  selecting `except`;
- a transfer path that bypasses required construction or destruction, or repeats
  a lifecycle transition without establishing a valid intervening state;
- possible self or interior alias conflict during replacement;
- contained reconstruction whose source aliases the payload it ends;
- complete `.=` without writable varying replacement authority or a viable
  replacement/fallback constructor;
- wrapper-owned `.=` with an invalid optional or variant target shape;
- partial-instance access or escape requiring a future unsafe control; and
- an inapplicable or unsupported future unsafe semantic assertion.

Exact identifiers, wording, presentation, and mandatory-versus-tooling
boundaries remain future diagnostic and safety work.

## Formatting

Canonical formatting should preserve:

- contextual adjacency in `+++ replacement`;
- visible construction-packet entry categories;
- left-to-right packet entry order;
- explicit positional intent;
- omission versus explicit type-default input;
- ordinary qualifier and parameter ordering;
- explicit `= default`, `= existing`, and `= forbidden`; and
- narrow unsafe-control documentation when those controls are later defined.

Formatters must not reorder packet entries because evaluation order is
observable. They must not add or remove explicit positional intent, convert
omission into type-default initialization, or parenthesize a bare multiple-result
invocation in a way that changes it into one expression.

## Boundaries and maturity

This document is current conceptual design, not formal grammar, an
implementation mapping, a compatibility contract, or a conformance
specification.

Later work may refine syntax and adjacent mechanisms while preserving:

- separate storage, member-lifetime, and complete-instance states;
- declaration-order automatic construction and reverse automatic destruction;
- automatic local and header lifetime ending and destruction order across normal
  and abrupt scope exits;
- scoped disposal remaining distinct from terminal destruction and completing
  before destruction of enrolled owned entries;
- direct body entry preserving active target-header lifetimes while establishing
  a fresh body scope and rerunning ordinary body operations from its beginning;
- the programmer-visible obligation to prove a live value before access through
  conditionally live storage;
- arbitrary control-flow order for explicit member lifecycle calls;
- static lifecycle tracking without mandatory runtime flags;
- shared ordinary-call input forms while `.member = expression` remains
  construction-specific;
- strict packet evaluation/binding order;
- constructor defaults after explicit packet inputs and before member
  construction;
- packet multiple-result forwarding remaining distinct from structural packing;
- ordinary and replacement constructors remaining resultless;
- demand-driven generated/existing/default/forbidden resolution;
- protected complete `.=` reconstruction requiring writable varying replacement
  authority for mutable or immutable values;
- custom replacement recycling old representation without enclosing `---` or
  `+++`;
- complete replacement on every normal return;
- terminal destruction authority;
- `.=` constructing into existing places, with first construction limited to
  empty result slots and explicitly controlled members, decided by the
  conservative first-construction rule;
- construction by an opaque operation asserted at that operation, with no
  declaration form that leaves storage indeterminate;
- every `.=` expression producing discardable access to the place it
  constructed, carrying the path's own qualifications;
- known pointer and alias hazards remaining visible;
- async, concurrency, ownership, and formal unsafe-control mechanisms remaining
  separate concerns until their focused reviews;
- differently represented type-family variants requiring suitable storage
  rather than assuming family-name compatibility;
- unmanaged unions retaining passive all-bit copy without member lifecycle;
- managed variants retaining wrapper-owned conditional payload lifecycle; and
- global and `once` initialization, retry, and teardown ordering remaining
  future lifecycle work.
