# Zax partials

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers adding behavior or state to types they did not write, and type authors deciding what others may add to theirs |
| Applies To | `partial` declarations, visibility grants, name resolution between a type and its partials, `seal` categories, added storage and its lifecycle, hook points, and storage added from other modules; not a formal grammar, ABI, or compiler mapping |
| Implementation State | Not established by this repository |
| Owns | The partial mental model; `expose partial` and `own partial`; name resolution among a type, its partials, and outside code; `seal` categories and their defaults; inclusion and lifecycle of partial storage; hook points and their calls; `seal open storage module` |
| Does Not Own | General abstract roles and fulfillment ([composition](composition.md)); general construction, replacement, and destruction ([construction and destruction](construction-and-destruction.md)); import injection itself ([namespaces and modules](namespaces-and-modules.md)); the intent category registry ([intent acknowledgements](intent-acknowledgements.md)); the execution context itself ([execution context](execution-context.md)) |
| Source / Provenance | Legacy partial-type notes and extension pressure, reconciled with current operator, composition, module, and lifecycle design |
| Supersedes | The retired root partial-types page |

## Adding to a type you did not write

Every user-defined operator belongs to its left receiver. That keeps operator
discovery predictable, but it leaves one direction unwritable:

```zax
myValue : MyValue
myInteger : Integer

a := myValue + myInteger   // valid: MyValue declares `+` with an Integer rhs
b := myInteger + myValue   // error: Integer is the receiver, and it declares no such `+`
```

`MyValue` can only contribute operators where it is the receiver. Nothing you
can write inside `MyValue` makes `Integer` the receiver of a new operation.

A **partial** adds declarations to an existing type, as if that type had
declared them:

```zax
MyIntegerOps :: partial Integer {
  operator binary '+' final : (
    result : MyValue
  )(
    rhs : MyValue
  ) = {
    // ...
  }
}
```

Declaring the partial does not change `Integer` anywhere by itself. Code sees
the partial's additions only where a scope asks for them:

```zax
scoreFor final : (
  result : MyValue
)(
  myInteger : Integer,
  myValue : MyValue
) = {
  Module.MyIntegerOps :: expose partial   // make MyIntegerOps visible in this block

  return myInteger + myValue             // valid: selects MyIntegerOps's `+`
}
```

Every other function, namespace, and module still sees `Integer` exactly as it
was.

The rest of this page builds on four ideas:

- **A partial only adds.** It cannot remove, hide, restrict, or replace anything
  the type declared.
- **A partial is invisible until granted.** Nothing is picked up merely because
  it exists or was compiled earlier.
- **The type decides what may be added.** Its `seal` clause opens or closes each
  kind of addition.
- **Added storage is different.** A stored member exists in every instance,
  wherever the instance goes, even where its name is invisible. So storage
  follows stricter rules than added functions.

## Making a partial visible

Suppose the `Geometry` namespace adds a function to `Point` through a partial:

```zax
namespace Geometry {
  MyPointOps :: partial Point {
    normalizeTo final : ()(length : Binary64) writable = {
      // ...
    }
  }
}
```

A **visibility grant** names that partial and makes its additions visible:

```zax
Module.Geometry.MyPointOps :: expose partial
```

It uses the ordinary declaration shape: the path on the left, the category on
the right. A grant applies from its position to the end of its block, including
nested blocks:

```zax
namespace MyGame {
  Module.Geometry.MyPointOps :: expose partial

  namespace Scoring {
    usePoints final : ()(myPoint : Geometry.Point writable &) = {
      myPoint.normalizeTo(10.0)   // valid: nested within the grant
    }
  }
}

namespace MyGame {
  reusePoints final : ()(myPoint : Geometry.Point writable &) = {
    myPoint.normalizeTo(10.0)     // error: this opening of MyGame has no grant
  }
}
```

A later reopening of a namespace does not inherit an earlier opening's grant.
Grants may appear in namespace openings, type bodies, partial bodies, and
function blocks, so you can keep one as narrow as the code that needs it.

### Partials that use other partials

A partial sees another partial only when its own body grants it:

```zax
MyPointScaling :: partial Geometry.Point {
  Module.Geometry.MyPointOps :: expose partial

  scaleToUnit final : ()() writable = {
    _.normalizeTo(1.0)           // valid: this body grants MyPointOps
  }
}
```

### A type author splitting their own type

A type body never resolves to its own partials unless it grants one. A type
author may still divide one type into partials in the same module and grant
them back into the type body. The type's own declarations still take precedence
over a granted partial's declarations of the same name. Only partials the type's
module can already name are available, so this lets an author organize their own
type. It does not let outsiders reach into it.

### Put grants at the top of a block

A grant written partway through a block could change what an identical call
means before and after it. That kind of silent change is exactly what grants
exist to prevent, so a grant that redirects a call is an intent error unless
acknowledged.

Here `MyFoo` declares `func` for a `U8`, and the `FooWide` partial adds one for
an `Integer`. A number such as `100` prefers `Integer` when both are candidates,
so granting `FooWide` changes which `func` the same call selects:

```zax
MyFoo :: type {
  func final : ()(value : U8) = { }
}

namespace Tools {
  FooWide :: partial MyFoo {
    func final : ()(value : Integer) = { }
  }
}

useIt final : ()(myFoo : MyFoo writable &) = {
  myFoo.func(100)                            // MyFoo's func(U8)

  intent<grant-redirected-selection>{
    Module.Tools.FooWide :: expose partial
  }

  myFoo.func(100)                            // FooWide's func(Integer), acknowledged
}
```

The `intent<...>{ }` braces create no scope, so the grant still applies to the
rest of `useIt`.

A **redirect** is a later use that would be valid without the grant but now
selects a different candidate. The error applies only to grants that are not at
the head of their block, in function blocks and namespace openings alike. A
grant that only makes new calls possible, or that sits at the head of its block,
needs no acknowledgement. The acknowledgement encloses the grant and covers
every redirect it causes.

### Naming a partial before or besides its declaration

A partial's name can be forwarded like a type's, so source can refer to it
before its declaration appears:

```zax
Module.Geometry.MyPointOps :: forward partial
```

The forward says only that this name is a partial. Exactly one later partial
declaration, or exact `alias partial`, completes it. Until then, a grant or an injection reference may
already name it, and checks that depend on its additions wait for the
completion, as with any [forward](declarations-and-bindings.md#forward-anchors).

An alias gives a partial another name:

```zax
PointOps :: alias partial Module.Geometry.MyPointOps
PointOps :: expose partial
```

The alias denotes the same partial and works wherever a partial path does:
grants, injection references, and completing a forward. It adds a name only; it
does not include the partial anywhere or make it visible by itself.

### Partials from another module

A partial crosses module boundaries the way a type does. Another module can
grant it only when its declaring module exports it, and whatever the partial
does not export stays invisible to importers:

```zax
Shapes :: import Module.GeometryLibrary

usePoints final : ()(myPoint : Shapes.Geometry.Point writable &) = {
  Shapes.Geometry.MyPointOps :: expose partial   // valid only if GeometryLibrary exports MyPointOps
  myPoint.normalizeTo(10.0)
}
```

As with any imported declaration, the path starts at the import's name.

## Names inside the type, inside the partial, and outside

A type and its partial may declare the same name, even for stored members. They
are two distinct declarations:

```zax
MyType :: type seal open storage {
  foo : Integer
}

MyPartial :: partial MyType {
  foo : Integer

  touch final : ()() writable = {
    _.foo = 1                  // the partial's own foo
  }
}
```

Which `foo` a name means depends on where the code is written:

| Code written in | Sees |
| --- | --- |
| The type's body | The type's own declarations, plus any partials the body grants |
| A partial's body | The partial's own declarations first, then the type's, plus any partials the body grants |
| Anywhere else | The type's declarations and every granted partial's declarations as one candidate set |

Outside both, ordinary selection applies to that combined set, and an ambiguity
is an error:

```zax
checkIt final : ()(myType : MyType writable &) = {
  Module.MyPartial :: expose partial

  myType.foo = 2               // error: ambiguous between MyType.foo and MyPartial.foo
}
```

A partial's name identifies a contribution, not a member of the type, so there
is no `myType.MyPartial.foo` path to disambiguate with. Repair the ambiguity by
granting the partial only where its names are needed.

The same applies to names a type publishes through an `own` member. Within one
type, a directly declared member takes precedence over a published name. A
partial is a separate scope, though, so if a type publishes `label` from an
`own` member and a granted partial declares its own `label`, uses of
`myType.label` outside are ambiguous.

### A partial's own names hide the type's

Inside a partial, its own declaration hides every type declaration with that
name. That includes a whole function family: a partial that declares any `func`
hides all of the type's `func` overloads inside the partial.

Ordinary [shadowing](declarations-and-bindings.md#shadowing) of an outer name
needs explicit permission, but this hiding does not. The type's author may later
add a member that happens to share a partial's name, and that must not break the
partial.

Hiding is complete by design: inside the partial, its own name always means its
own member, whatever the type adds later, so no path reaches the hidden
original. When
a partial truly needs it, call a helper declared in a scope without the grant,
where only the type's member is visible.

These rules keep failures loud. When a type later adds a name a partial already
uses, nothing changes inside the partial, and outside code that grants the
partial gets an ambiguity error instead of silently switching members.

### What a partial cannot see or be

- **No private access.** A partial does not see the type's `private` members. A
  type that grants its own partial does not see the partial's `private` members
  either. Zax has no friendship role.
- **A partial's name is not a type.** `MyPartial` names a contribution for grants
  and export. You cannot declare a value of type `MyPartial`.
- **Exposure fences apply to the type's own surface.** A type's
  [`= forbidden family` fence](composition.md#fencing-generated-behavior)
  controls what the type itself generates, exposes, and declares. It does not reach a partial's declarations; use `seal`, below,
  to control what partials may add.

## What a type allows others to add

A type opens or closes each kind of addition with a `seal` clause:

```zax
MyRecord :: type {
}
// Defaults: others may add functions, nested types, and `once` state.

MyCatalog :: type seal open storage {
}
// Also accepts added stored members.

MyFixed :: type seal {
}
// Nothing may be added.

MyLocked :: type seal open storage close callable {
}
// Accepts stored members, but no added functions.
```

| Category | Covers | Default |
| --- | --- | --- |
| `callable` | Added functions and operators, including phrases and mixfix | Open |
| `nested` | Added nested `type`, `enum`, `union`, and `variant` declarations, type aliases, and [reshapes](terms.md#reshape) | Open |
| `once` | Added `once` state | Open |
| `storage` | Added stored members and per-instance callable slots | Closed |
| `module` | Whether storage may come from other modules; see [storage from any module](#storage-from-any-module) | Closed |

A `seal` clause starts from the defaults and changes only the categories it
names. In `seal open storage close callable`, each `open` or `close` applies to
the categories written after it. Bare `seal`, naming no categories, closes every
category.

**Storage is closed by default** because opening it has costs for everyone who
uses the type. If every type accepted added members, no library could know any
type's size or layout, and every hand-written copy would have to anticipate
members it has never seen. A type that is designed to carry extra state opens
storage deliberately.

**Some additions need several categories.** A `varying` callable adds a
per-instance slot, so it needs both `storage` and `callable` open. A `once`
callable needs both `callable` and `once`.

**Categories apply only to what a partial adds directly.** A nested type that a
partial adds has its own contents, callables included, even when direct callables
are closed on the extended type. Adding direct callables does not require
`nested` to be open.

### Language-provided types

Language-provided types, such as `I64`, `Boolean`, and `String`, are closed to
storage and open to callables. The one exception is `ExecutionContext`, described
under [storage from any module](#storage-from-any-module). That is what makes `MyIntegerOps` above possible.
Protected signatures stay protected: a partial cannot declare an operation whose
every operand belongs to a closed intrinsic family, or replace a
language-provided form. See
[protected intrinsic domains](operators.md#protected-intrinsic-domains).

A partial targets the identity its named type resolves to. `Integer` is a
profile-selected alias, so `partial Integer` extends whatever `Integer` means on
the current profile. A partial on `Integer` and another on `I64` can conflict on
a profile where `Integer` is `I64` and coexist on one where it is not.

### Enums, variants, and unions

A partial cannot add enum members, variant alternatives, or union lenses:

```zax
Color :: enum U8 {
  Red
  Green
}

MyShades :: partial Color {
  Teal                          // error: a partial cannot add enum members
}
```

An enum or variant promises a fixed set of values. Code elsewhere relies on
that set; an exhaustive `switch` in a library would not know about `Teal`, yet a
`Teal` value could still reach it. A union's lenses define its shape and safety,
and a partial must not make a safe union unsafe. Use `unsafe cast` when you need
another view of existing storage.

Partials may still add callables and nested declarations to enums, variants, and
unions, subject to each type's `seal` clause.

## Adding storage

An added stored member exists in every instance of the type, even in code that
cannot name it:

```zax
MyTagging :: partial MyCatalog {
  tag : String = "none"
}
```

Every `MyCatalog` now carries `tag`. A library that received a `MyCatalog` from
you would copy, move, and destroy `tag` along with everything else. For that to
work, the complete set of added members must be known before the type's layout
is final. Zax therefore accepts storage additions from only two places:

- **the type's own module**, where partials are included automatically; and
- **an import injection body**, where an importer includes partials into the
  imported module's types.

In the type's own module, the layout is final when the module is finalized, the
same point by which every forward must be completed. An importer sees the final
layout as soon as the import completes.

Added storage changes the type's shape, so
[structural compatibility](structural-shapes-and-compatibility.md) that
depended on the old shape can stop holding. Suppose `MyCatalog` and another type
with the same members were compatible for a same-storage view. Once `MyTagging`
adds `tag`, their shapes differ and that view is no longer valid. This is an
expected consequence of adding storage, not an error the language prevents. It
is one of the costs a type author accepts by writing `seal open storage`.

### Including a partial through an import

An [import injection body](namespaces-and-modules.md#inject-declarations-before-module-source)
can include a partial written elsewhere. Here the library's `MyCatalog` writes
`seal open storage`:

```zax
Library :: forward module

namespace MyNamespace {
  MyTagging :: partial Library.MyCatalog {    // checks wait until Library completes
    tag : String = "none"
  }
}

Library :: import Module.LibraryDefinition {
  Module.MyNamespace.MyTagging :: own partial
}
```

`own partial` includes the partial's storage and [hook](#hooks) fulfillments in
the library's type. The library's own source still cannot see the partial's names,
so the library compiles exactly as before, except that its lifecycle now covers
`tag`. To use `tag` yourself, grant `MyTagging` where you need it.

Defining the partial directly inside the injection body has the same effect,
because partials stay invisible without a grant even in the module where they
are declared.

`expose partial` in an injection body also includes the partial, and in
addition makes its names visible to all of the library's source:

```zax
Library :: import Module.LibraryDefinition {
  intent<injected-partial-exposure>{
    Module.MyNamespace.MyTagging :: expose partial
  }
}
```

The library's existing calls may now select the partial's overloads, and you
cannot see the library's code to know whether they do. That is why this form
always requires an acknowledgement, whether or not a redirect actually happens.
Prefer `own partial` unless the library is meant to use your additions.

A few consequences:

- An alias alone does not include a partial; it only adds another name.
- A partial that adds storage but is never included is an error, because its
  storage could never exist anywhere.
- Every import creates its own
  [module instance](namespaces-and-modules.md#generative-imports-and-injection)
  with its own types. A partial targets one of them, such as
  `Library.MyCatalog`, so it cannot be included into a second import of the same
  module by accident.
- Hook fulfillments are also included this way. A partial that adds no storage
  but fulfills hooks still needs inclusion.
- Including a partial that is already included changes nothing. That covers
  `own partial` naming a partial declared in the type's own module, or one
  defined in the same injection body. It is legal, and tooling may flag it as
  redundant. `expose partial` in an injection body is never redundant in this
  way, because it also makes the partial visible to the library's source.

In composition, `own` contains a member and publishes its names on the
container. `own partial` does neither: it includes the partial's storage and
hook fulfillments without publishing any names into the library.

### Construction and destruction of added storage

A partial may declare one constructor: a no-argument `+++` that establishes its
own storage. The compiler runs it automatically. Any other constructor in a
partial is an error, because nothing could ever call it:

```zax
MyTagging :: partial MyCatalog {
  tag : String

  +++ final : ()() = {
    _.tag .= "none"                      // construct tag directly
  }

  +++ final : ()(initial : String) = {   // error: nothing can call a partial's argument constructor
  }
}
```

A partial's `---` likewise runs automatically for its own storage.

The order is fixed:

1. The type's automatic members are constructed.
2. Each partial's storage is constructed, in partial definition order.
3. The type's constructor body runs, including any hook calls.

Destruction mirrors it: the destructor body and its hook calls, then each
partial's storage in reverse, then the type's members in reverse.

Definition order is source order. Partials included through injection come
before the owner module's own partials, because an injection body is processed
before the module's own source. The order is defined, but code should not rely
on it, and a partial should not assume another partial's storage is already
constructed.

### Copying and assigning

The compiler-generated copy constructor and `=` include every partial's
storage. A type that uses the generated operations copies added storage
correctly with no further work.

A hand-written copy constructor or `=` does not copy added storage by itself:

```zax
MyCatalog :: type seal open storage {
  entries : Integer

  operator binary '=' final : (
    result self : MyCatalog &
  )(
    rhs : MyCatalog readonly &
  ) writable = {
    _.entries = rhs.entries
    // MyTagging's tag is not copied: this body cannot name it
    return _
  }
}
```

This is not an error. `=` is domain-specific, and its author may intend exactly
this. A type that wants partials to take part calls a hook; see
[Hooks](#hooks).

A partial may also declare `=` itself. If that collides with the type's `=`,
the result is an ordinary ambiguity.

### Replacement

[Replacement](construction-and-destruction.md#reconstructive-replacement) with
`.=` ends a value's lifetime and establishes a successor in the same place.
During every replacement of the type, including the
[generated fallback](construction-and-destruction.md#generated-fallback), each
partial's storage is either reset or kept:

- By default, the partial's storage receives `---` and then its `+++`, so `tag`
  below would return to `"none"`.
- If the partial declares a no-argument `+++ replacement`, the compiler calls it
  instead. Like any replacement constructor, it starts with the partial's
  storage still live, holding the previous state, so an empty body keeps it.

```zax
MyTagging :: partial MyCatalog {
  tag : String = "none"

  +++ replacement final : ()() = {
    // keep tag unchanged across MyCatalog's replacement
  }
}
```

A replacement constructor with parameters in a partial is an error, because
nothing could supply its arguments. A partial that needs the incoming values
uses a hook that the type's own replacement constructor calls.

## Hooks

A type cannot call a partial's functions, because its body never sees its
partials. A **hook point** is the one deliberate route from a type to its
partials: a contract of `abstract` roles that the type calls and that any
number of partials may fulfill.

```zax
MyCatalog :: forward type

MyCatalogHooks :: type {
  construct abstract optional : ()()
  assign abstract : ()(rhs : MyCatalog readonly &) writable
}

MyCatalog :: type seal open storage {
  hooks partial : MyCatalogHooks
  entries : Integer

  +++ final : ()() = {
    _.hooks.construct()
  }

  operator binary '=' final : (
    result self : MyCatalog &
  )(
    rhs : MyCatalog readonly &
  ) writable = {
    _.entries = rhs.entries
    _.hooks.assign(rhs)
    return _
  }
}

MyTagging :: partial MyCatalog {
  tag : String = "none"

  onAssign fulfill hooks.assign private final : ()(
    rhs : MyCatalog readonly &
  ) writable = {
    _.tag = rhs.tag
  }
}
```

Now `MyCatalog`'s hand-written `=` copies `tag` too.

`hooks partial : MyCatalogHooks` declares the hook point. A partial takes part by
naming the hook point's roles with `fulfill`, just as a composition fulfillment
names the path of the role it satisfies. `MyTagging` fulfills the required
`hooks.assign` and leaves the optional `hooks.construct` unfulfilled. A partial
can name only a hook point it can see, so a hook point meant for partials is not
`private`.

Because a role is identified by its path, one type can offer several hook points
with the same contract, and a partial chooses among them by name:

```zax
MyCatalog :: type seal open storage {
  primaryHooks partial : MyCatalogHooks
  auditHooks partial : MyCatalogHooks
  // ...
}

MyAuditing :: partial MyCatalog {
  onAudit fulfill auditHooks.assign private final : ()(
    rhs : MyCatalog readonly &
  ) writable = {
    // ...
  }
}
```

How hook points behave:

- **Zero or more fulfillments.** A partial that fulfills any role of a hook
  point must fulfill every required role of that hook point, and may fulfill
  each optional role once. A partial that names none of its roles does not take
  part. A call such as `_.hooks.assign(rhs)` runs every included fulfillment.
  If no partial fulfills a role, the compiler removes the call.
- **Explicit fulfillment only.** A partial's function participates only when it
  names the role with `fulfill`. A matching signature is not enough.
- **Order.** Fulfillments run in partial definition order. A hook called from
  the type's `---` runs them in reverse, mirroring construction. Do not rely on
  either order.
- **Callable roles without results.** Hook calls return nothing. Calling a role
  that has a result through a hook point is an error, because there is no way to
  combine several answers. For the same reason, a hook-point contract cannot
  contain value roles.
- **The receiver is the instance.** A hook body is
  [`bound`](declarations-and-bindings.md#bound-and-unbound-function-storage), so
  `_` is the `MyCatalog` instance and needs no extra parameter.
- **Private state through parameters.** A hook body sees only the type's
  non-private surface. When a partial needs private state, the type passes it as
  hook parameters, which makes the contract the type's deliberate interface to
  its partials.
- **`final` or `varying` fulfillment.** A role that writes no stance has an
  ordinary `varying` type side, and a `final` fulfillment is compatible with
  it, as described in
  [composition](composition.md#final-fulfillment-of-a-callable-role). A
  `varying` fulfillment adds a per-instance slot, so in a partial it needs
  `storage` and `callable` open.

A construct hook runs wherever the type's constructor body calls it. By then
every partial's own `+++` has already established its storage, so the hook can
build on it.

The general abstract-role model, including optional roles and `fulfill`, is
defined by [composition](composition.md#abstract-roles-and-explicit-fulfillment).

### A declared hook the type never calls

Declaring a hook role that the type's own body never invokes is an intent error:

```zax
MyCatalog :: type seal open storage {
  intent<uninvoked-hook-role>{
    hooks partial : MyCatalogHooks    // neither role is invoked by MyCatalog
  }
}
```

There are legitimate reasons for it. A contract may be shared by several types
that each use only some roles, or the calls may not be written yet. The error
concerns the type's calls, so it applies to optional and required roles alike.
The acknowledgement encloses the hook-point declaration and covers every role of
that hook point the type never invokes.

Tooling may also help a partial's author notice when a type's copy or `=` is
hand-written and calls no assign hook, so that added storage is not copied
through it. That is advice for tools, not a required diagnostic.

### Asking at compile time whether a hook is fulfilled

Preparing for a hook call can cost something even when no partial fulfills the
hook. A type may want to skip that preparation when nobody is listening.
[Compile-time code](compile-time-code.md#choosing-code-at-compile-time) can ask:

```zax
// Illustrative query spelling; the exact form is future work.
+++ final : ()() = {
  if compile MyCatalog.hooks.construct is fulfilled {   // asks about the type, not an instance
    prepareHookState()   // this cost exists only when some partial fulfills the hook
  }
  _.hooks.construct()
}
```

The question counts included fulfillments, not merely visible ones, so its
answer is known only once fulfillment is fixed: when the type's module
finalizes, or for an importer, when the import completes. A question asked
before then waits for that point, the same way a `size of` on a type with
pending storage waits. In the example, the constructor's compile point is
answered once `MyCatalog`'s module has finished, and the compiler never guesses
an earlier answer.

Waiting is an error only when the answer could change what it is waiting for:
for example, when the branch the question selects would declare or include a
partial that fulfills the same hook. The compiler reports that cycle.

A type declared with `seal open storage module` has its fulfillments fixed only
when the whole application is assembled, which is after all compile-time code
has run. For such a type the question cannot be answered at compile time and is
an error; check at runtime instead, or close the type's storage.

## `once` in partials

A partial may add `once` state unless the type writes `seal close once`. That
state belongs to the module instance that declares the partial, not to the
module that declares the type.

## Storage from any module

Normally the complete storage of a type is known once its own module and its
importers' injection bodies are processed. `seal open storage module` allows
storage partials from any module's own source instead, which means the type's
layout is final only when the whole application is assembled:

```zax
intent<application-closed-storage>{
  MyRegistry :: type seal open storage module {
  }
}

MyCache :: type {
  registry : MyRegistry     // MyCache's layout now also waits for the whole application
}
```

**The delay spreads.** Every type that embeds such a type by value inherits the
pending layout, and so does every array of it or generic instantiation over it.
Any `size of` in compile-time code that touches those types waits too, and a
compile-time value of such a type cannot become a constant in the program until
its layout is final. A library
that uses `MyCache` cannot finalize its own layouts until the whole program is
known. That is why the declaration needs an acknowledgement, and why this form
is rarely the right choice.

The execution context is the case it exists for. The runtime constructs every
context instance, and code reaches its members by path rather than embedding
the context by value, so the delay does not spread. Its additions are described
by [execution context](execution-context.md).

`module` applies only to storage. `seal close storage open module` has nothing
to act on and is an error that must be rewritten; it cannot be acknowledged.

## Costs

Programmers must be able to discover:

- storage added to a type by each included partial, and its size and alignment
  effect;
- automatic construction, destruction, copying, and replacement work performed
  for partial storage;
- hook calls and the fulfillments they run;
- per-instance slots added by `varying` fulfillments or members;
- `once` state added by partials; and
- layouts left pending until application assembly by `seal open storage module`.

## Diagnostics

Diagnostics should distinguish:

- a use of a partial's name where no grant makes it visible;
- a grant of a partial that is not exported to this module;
- ambiguity between a type's declaration and a granted partial's declaration;
- an addition the type's `seal` clause does not allow, naming the category;
- a partial constructor or replacement constructor with parameters;
- enum member, variant alternative, or union lens additions;
- a storage partial that is never included;
- a mid-block grant that redirects a call without acknowledgement;
- an injected `expose partial` without acknowledgement;
- an uninvoked hook role without acknowledgement;
- a hook call to a role that has a result, or a value role in a hook-point
  contract;
- a required hook role left unfulfilled by a partial that fulfills another role
  of the same hook point;
- `seal open storage module` without acknowledgement; and
- `seal close storage open module`.

A diagnostic about a partial should name the original type and each
contributing partial, and explain why a partial was or was not visible at a use.

## Source stability

The following changes are source- or behavior-visible:

- granting a partial may add candidates, which can change which declaration a
  use selects or introduce an ambiguity;
- adding a name to a type may make uses outside the type ambiguous where a
  partial with that name is granted;
- adding, removing, or reordering storage partials changes layout, structural
  compatibility, and lifecycle work;
- opening or closing a `seal` category changes which partials are valid;
- adding a required role to a hook contract breaks partials that fulfill any of
  its roles; and
- exporting or no longer exporting a partial changes which modules can grant it.

Source order never breaks an ambiguity between partials.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, ABI, or
compiler mapping.

Future work owns:

- whether generic code sees partials visible where the generic is defined or
  where it becomes concrete, and generic partials;
- partials generated by the language, the compiler, or a CPU provider;
- where the `seal` clause sits relative to other declaration words;
- the exact source spelling for asking whether a hook point has fulfillments;
- initialization and teardown order of `once` state added by partials;
- reflection of partial contributions and their provenance;
- exact diagnostic presentation; and
- carrying execution-context additions across async suspension.
