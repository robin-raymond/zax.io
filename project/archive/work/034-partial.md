# 034: `partial`

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `034` |
| Created | 2026-09-25 |
| Completed | 2026-09-26 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted partials, type-definition, construction, composition, declarations, namespaces, intent-acknowledgement, execution-context, integer, identity, operator, phrase, mixfix, casting, and terms owners |

## Non-authority notice

This file is a collaborative working record. Existing documentation, candidate
syntax, examples, and later aligned findings remain non-authoritative until a
separately discussed, aligned, and explicitly authorized promotion incorporates
them into their lasting owners.

## Fixed initiating input

This section records the aligned information known when work item `034` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

What should `partial` mean in Zax, and how can it be designed so that the
capability it opens stays bounded and predictable?

A partial reopens a type that is not sealed and adds declarations to it:
stored members, fixed or varying functions, operators, and nested types or
enums. The maintainer's illustrative starting shape:

```zax
// Illustrative syntax from the maintainer's note.
MyPartialInteger :: partial Integer {
  operator binary '+' final : (result : ResultType)(rhs : MyType) = {
    //...
  }
}
```

### Motivating pressure

- **Receivers that cannot be extended.** Every user-defined operator belongs to
  its receiver type, so `myType + myInteger` can be declared on `MyType` but
  `myInteger + myType` cannot be declared anywhere. A partial on `Integer`
  could add that operator as if `Integer` had declared it.
- **The execution context.** Every thread receives a `___` context instance
  holding built-in defaults such as the allocation arena. The context is
  intentionally not sealed: programmers need to add their own functions and
  stored members to it, including per-thread storage. This is a hard need, not
  a convenience.

### Starting input

The maintainer's newest thinking is
[`project/raw/maintainer-notes/partial.md`](../raw/maintainer-notes/partial.md).
It replaces the legacy framing where they differ. The legacy
[`project/raw/partial-types.md`](../raw/partial-types.md) remains evidence for
insights and constraints but is not authoritative over the concept or the
maintainer's note.

The note identifies these complexities:

- conflicting names between partials, and names that become ambiguous after
  code that used them was already compiled and linked;
- added stored members, which block `size of` until the type is complete;
- construction, destruction, assignment, and replacement that cannot know about
  storage added later;
- ordering across modules; and
- whether one module's additions are visible to another.

It proposes these starting points, all open to better syntax and spelling:

- **Order-sensitive visibility through `forward`.** A use considers only
  partials already defined, or explicitly forwarded within the visible nesting,
  when it compiles. Later partials do not change what earlier code sees.
- **Graded sealing,** for example `seal storage`, `seal callable`, and
  `seal once`, with bare `seal` sealing everything.
- **Hooks through an abstract contract.** A type calls declared hook points in
  its own lifecycle and operations; partials that explicitly implement the
  contract are linked to them; with no partials, the calls compile away. Hooks
  return nothing, and their order is defined but must not be relied upon.
- **Delayed `size of`,** with an earlier opt-in seal, and likely denying
  `partial` on types that must be complete for compile-time work.
- **A named type for the context.** `___` needs a system type name within a
  namespace so a partial can target it. The name can be chosen now, with the
  namespace left as future pressure, as for `OpaqueObserver`.

### Deliberately unresolved framing

- **Order independence versus order-sensitive visibility.** The legacy input
  repeatedly requires that import, declaration, and build order never resolve
  conflicts or select a surface. The maintainer's note proposes that
  compilation order and `forward` visibility decide which partials a use sees.
  Reconciling or choosing between these is central to this item.
- Who may declare a partial on intrinsic, identity, foreign-owned, or
  language-owned types, and whether an owner must opt in.
- Whether and how partials may add stored members, and how those members take
  part in construction, destruction, copy, assignment, and replacement.
- How partials interact with protected signatures, composition, enums, unions,
  variants, and structural shape.
- The context type's name.

### Starting boundaries

- Do not design async context propagation. The note marks it as a separate
  concern.
- The ordering of words after `+++` and unsafe-category syntax are separate
  deferred pressures, captured in the
  [cross-cutting audit](../raw/cross-cutting-audit.md) and the
  [analysis-control input](../raw/analysis-controls.md).

### Initial stopping guidance

Creating and routing this work item does not authorize analysis. A later
assignment begins it.

Do not promote findings, change current owners, archive this work item, or
create work item `035` without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, promotion, deferral, and closure.
- [Maintainer notes on `partial`](../raw/maintainer-notes/partial.md) - the
  primary input and the maintainer's current direction.
- [Raw partial type extensions](../raw/partial-types.md) - legacy constraints
  and pressures to test against the note: protected signatures, intrinsic and
  identity authority, composition merges, candidate relatching, the
  execution-context shape, and enums.
- [Zax execution context](../../language/execution-context.md), the
  [one context shape](../../language/execution-context.md#one-context-shape-one-current-instance-per-thread)
  section - the current context model that partials must extend.
- [Zax type definitions](../../language/type-definitions.md), the
  [what a type body can contribute](../../language/type-definitions.md#what-a-type-body-can-contribute),
  [forward anchors](../../language/type-definitions.md#forward-anchors), and
  [partial and generic completion boundaries](../../language/type-definitions.md#partial-and-generic-completion-boundaries)
  sections - what a partial adds to and when a type is complete.
- [Zax declarations and bindings](../../language/declarations-and-bindings.md#forward-anchors),
  forward anchors, and
  [Zax namespaces and modules](../../language/namespaces-and-modules.md), the
  [visibility and export baseline](../../language/namespaces-and-modules.md#visibility-and-export-baseline)
  and [forward module and namespace roots](../../language/namespaces-and-modules.md#forward-module-and-namespace-roots)
  sections - the `forward` and visibility rules the note's proposal depends on.

### Consequence-driven

Read only the smallest relevant sections when the concern reaches them:

- [Zax operators](../../language/operators.md) and
  [Zax operator phrases](../../language/operator-phrases.md) for receiver
  ownership, discovery, and protected signatures.
- [Zax composition](../../language/composition.md#abstract-roles-and-explicit-fulfillment)
  for `abstract` roles, fulfillment, and `own`, which the hook proposal uses.
- [Zax construction, replacement, and destruction](../../language/construction-and-destruction.md)
  when added storage meets lifecycle operations.
- [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  and [Zax arrays and slices](../../language/arrays-and-slices.md) for layout
  and `size of`.
- [Zax identity types](../../language/identity-types.md),
  [Zax integers](../../language/integers.md), and
  [Zax enums](../../language/enums.md) for intrinsic, identity, and enum
  extension.
- [Zax function invocation](../../language/function-invocation.md#candidate-selection)
  for candidate relatching and ambiguity.
- [Zax conversions and casts](../../language/casting.md#your-types-and-the-languages-scalars)
  for `as` on scalar receivers.
- Raw inputs on [compile-time execution](../raw/compile-time-execution.md),
  [reflection](../raw/reflection.md),
  [global and once lifetimes](../raw/global-and-once-lifetimes.md),
  [library surface and namespaces](../raw/library-surface-and-namespaces.md),
  and [export and visibility directives](../raw/export-and-visibility-directives.md)
  when their concerns are reached.

### Audit-only

- Archived numbered work, only when a provenance question cannot be answered
  from current owners or raw input.

## Working record

### Status

The maintainer and agent reviewed the initial reconstruction in chat on
2026-09-25 and 2026-09-26. The findings under [Aligned
findings](#aligned-findings) are aligned for this review scope. They remain
non-authoritative until a separately authorized promotion places them in their
lasting owners. Partial syntax in every example is illustrative. The
[initial reconstruction](#initial-reconstruction) is retained below as evidence;
where it differs from the aligned findings, the aligned findings govern, and
[Discarded or superseded](#discarded-or-superseded) lists what was replaced.

### Model in brief

A partial is a named, add-only contribution to an existing concrete type. It is
invisible until a scope explicitly grants it. Inside the type, the type's own
declarations win. Inside the partial, the partial's own declarations win.
Outside both, the type and every granted partial form one candidate set under
ordinary selection, and an ambiguity is an error.

What a partial may add is controlled by the type's `seal` clause, one category
at a time:

| Category | Covers | Default |
| --- | --- | --- |
| `callable` | Added functions and operators, including phrases and mixfix | Open |
| `nested` | Added nested `type`, `enum`, `union`, and `variant` declarations, type aliases, and reshapes | Open |
| `once` | Added `once` state | Open |
| `storage` | Added stored members and per-instance callable slots | Closed |
| `module` | Whether storage and hook fulfillment may come from other modules' own source | Closed |

Storage and hook fulfillment change what every instance contains or does. They
come only from partials declared in the owner's own module, or partials
included through an import injection body, so they are known before the type
completes. `open module` widens that to the whole application, with a strongly
cautioned layout delay.

A type reaches its partials only through **hooks**: `abstract` contract roles
that the type calls and that zero or more partials fulfill.

### Aligned findings

#### Sealing and defaults

A type states which categories are open or closed after `seal`. Categories not
written keep their defaults. Bare `seal` closes every category. A mixed clause
reads left to right.

```zax
MyRecord :: type { }                               // callable, nested, once open; storage, module closed
MyExtensible :: type seal open storage { }         // storage additions allowed through import injection
MyFixed :: type seal { }                           // nothing may be added
MyLocked :: type seal open storage close callable { }
ExecutionContext :: type seal open storage module { }  // storage from any module; application-closed
```

- An addition that touches several categories needs every one of them open. A
  `varying` callable needs `storage` and `callable`. A `once` callable needs
  `callable` and `once`.
- `open module` requires `open storage`. Two separate rules apply:
  - `seal close storage open module` is a non-acknowledgeable intent error. The
    modifier has nothing to act on, so it must be rewritten.
  - `seal open storage module` is valid but has application-wide consequences.
    It is an acknowledgeable intent error. The acknowledgement encloses the
    complete type declaration:

    ```zax
    intent<application-closed-storage>{
      MyRegistry :: type seal open storage module {
      }
    }
    ```

    The language-provided `ExecutionContext` is not written in programmer
    source, so it needs no acknowledgement. The category name is provisional.
- Categories apply only to what a partial adds directly. A nested type that a
  partial adds has its own contents, including callables, even when direct
  callables are closed on the extended type. Adding direct callables does not
  require `nested` to be open.
- `seal` rather than a separate `open` keyword introduces the clause. `close`
  is presumed for storage and module when not specified.
- The category word is `nested`. `nesting` was avoided because it already
  describes a grant's scope.

**Language-provided types** keep `storage` closed and are open to callables. At
promotion, the [integers](../../language/integers.md#exact-intrinsic-family)
statement that each realized specialization is "sealed against ordinary
extension", and the [terms](../../language/terms.md#sealed-type) entry, must be
corrected to "sealed to storage". Language namespaces never accept import
injection.

**Aliases resolve.** A partial targets the identity its named type resolves to.
`partial Integer` extends whatever `Integer` aliases on the current profile. A
partial on `Integer` and one on `I64` can therefore conflict only on profiles
where the two coincide. That portability consequence is accepted and must be
taught.

**A partial on a generic** would be a generic partial, which belongs to generic
work. This item covers partials of concrete types.

#### Visibility grants

A partial is invisible until a scope grants it:

```zax
namespace A {
  Module.E.F.MyPartialType :: expose partial

  namespace B {
    myValue.myPartialFunc()      // valid: nested within the grant
  }
}

namespace A {
  myValue.myPartialFunc()        // error: this opening has no grant
}
```

- The form follows ordinary declaration shape: the path on the left, the
  category on the right.
- A grant applies from its position to the end of its block, including nested
  blocks. It does not apply before its position, and a later reopening of the
  same namespace does not inherit it.
- Grants may appear in namespace openings, type bodies, partial bodies, and
  function blocks.
- A partial from another module can be granted only if that module exports it.
  Partials need export just as types do, and unexported contents of a partial
  stay invisible to importers.
- **Partial to partial.** A partial sees another partial only when its own body
  grants it.
- **Inside the original type.** A type body may grant one of its own partials by
  explicit opt-in. The type's own declarations still win. Without a grant, a
  type never resolves to a partial. Only partials already visible to the type's
  module can be named there, so this serves a type author splitting their own
  type rather than outsiders.
- **Teaching risk.** Composition already uses `expose` as a member qualifier
  (`engine expose : Engine`). The pairing `expose partial` distinguishes the
  grant, but the difference must be taught.

**A mid-block grant that redirects a call is an acknowledgeable intent error.**
A redirect is a later use that would be valid without the grant but now selects a
different candidate. The error applies only to a grant that is not at the head of
its block, in function blocks and namespace openings alike. A grant at the head
of its block never triggers it. The acknowledgement encloses the grant
declaration and covers every redirect it causes. The diagnostic points at both
the grant and each redirected use.

```zax
useIt final : ()(myFoo : MyFoo writable &) = {
  myFoo.func(100)                          // MyFoo's func(U8)

  intent<grant-redirected-selection>{
    Module.E.F.FooWide :: expose partial
  }

  myFoo.func(100)                          // FooWide's func(Integer), acknowledged
}
```

Without the enclosure, the second call is an intent error. The category name is
provisional until the maintainer's collective naming review. It avoids a
`partial-` prefix because `partial-enum-selection` and `partial-variant-selection`
already use "partial" to mean "incomplete".

#### Name resolution

A type and a partial may declare the same name, including the same stored
member name. They are distinct declarations, not a conflict:

```zax
MyType :: type {
  foo : Integer
  // MyPartial.foo : Integer   // conceptual only, not syntax: distinct storage
}

MyPartial :: partial MyType {
  foo : Integer

  touch final : ()() writable = {
    _.foo = 1                  // the partial's own foo
  }
}

// Outside, where MyPartial is granted:
myType.foo = 2                 // error: ambiguous between MyType.foo and MyPartial.foo
```

- **Inside a partial**, its own declarations win, then the type's. Hiding is by
  name: a partial that declares any `func` hides the type's whole `func` family
  inside the partial.
- The partial cannot see a hidden original, and no `shadowable` is required.
  The original type may add a same-named member later, and that must not break
  the partial. To reach the hidden original, call a helper declared in a scope
  without the grant.
- **Outside**, the type's declarations and every granted partial's declarations
  form one candidate set. Ordinary selection applies. An ambiguity is an error;
  the compiler does not guess.
- There is no qualification by partial name, because partial names are
  namespaced paths that could themselves be ambiguous. Repair an external
  ambiguity by narrowing where the partial is granted.
- These rules fail safely in both directions. A later same-named member on the
  type changes nothing inside the partial. Outside, it produces an ambiguity
  error rather than a silent switch.
- **A partial's name** is a namespace-like handle used only to select the
  partial for opt-in visibility and export. It is not a type and does not exist
  on its own.
- **No private access.** A partial does not see the type's `private` members, and
  a type that grants its own partial does not see the partial's `private`
  members. Zax has no friendship role.

#### Constructors, replacement, and `=`

- A partial may declare only a no-argument `+++`. It runs automatically to
  establish that partial's own storage. Any other constructor form in a partial
  is an error: nothing can invoke or hook it. The partial's `---` likewise runs
  automatically for its own storage.
- A partial may declare a no-argument `+++ replacement`, which runs
  automatically for its storage during every replacement of the type.
  Otherwise its storage receives `---` then `+++`. A partial replacement with
  parameters is an error. (Revised during the dry run; see its result.)
- `=` is not protected. A partial may declare it. If that collides with the
  type's `=`, the result is an ordinary ambiguity, which the author asked for.
- The compiler-generated copy and `=` include every partial's storage, so a type
  using the generated operations copies partial storage correctly without hooks.
- A hand-written copy or `=` does not automatically copy partial storage. It
  reaches partial storage only by calling hooks. Failing to call one is not an
  error, because `=` is domain-specific and the author may intend it.

#### Storage closure and lifecycle order

- Storage additions and hook fulfillments are included in a type only from two
  sources. The complete set is therefore known before the type completes, and
  each generative module instance has its own set.
  - **Partials declared in the owner's own module** are included
    automatically, for example when a type author splits their own type. That
    module's storage closes at module finalization, the same point by which
    every forward must complete.
  - **Partials included through an import injection body**, in one of three
    ways:

  | Written in an injection body | Adds storage and hook fulfillments | Library source sees the partial's surface |
  | --- | --- | --- |
  | A partial *defined* in the body | Yes | No: partials stay invisible until granted |
  | `Path :: own partial` | Yes | No |
  | `Path :: expose partial` | Yes | Yes: a grant at the head of the library's root |

  Defining a partial in the body is already the `own` case, because injected
  declarations behave as if the target author wrote them, and partials are
  invisible without a grant. `own partial` includes a partial written
  elsewhere by reference:

  ```zax
  Library :: forward module

  namespace MyNamespace {
    MyTagging :: partial Library.MyType {    // checks pending until Library completes
      tag : String = "none"
    }
  }

  Library :: import Module.LibraryDefinition {
    Module.MyNamespace.MyTagging :: own partial   // Module resolves in the importer here
  }
  ```

  The library compiles exactly as before, except that generated lifecycle
  covers the added storage and the library's hook points reach the partial's
  fulfillments. The importer still grants `MyTagging` in its own scopes to use
  `tag`.

  `expose partial` in an injection body also makes the partial's surface
  visible to all of the library's source. The library's existing calls may then
  silently select the partial's overloads, and the importer cannot see the
  library's code to know whether that happens. It is legal but not
  recommended. It is an acknowledgeable intent error on the injected grant
  itself, whether or not a redirect actually occurs, so the importer does not
  have to fork the library to acknowledge anything:

  ```zax
  Library :: import Module.LibraryDefinition {
    intent<injected-partial-exposure>{
      Module.MyNamespace.MyTagging :: expose partial
    }
  }
  ```

  The category name is provisional. `own partial` needs no acknowledgement.
  Teaching must present `own` as the recommended route and explain what
  `expose` additionally risks.

  Inclusion details:

  - An alias alone does not include a partial. It only adds a name.
  - A partial declared outside the import can target a type in the imported
    module through `Library :: forward module`. Its checks remain pending
    until the import completes.
  - A partial that adds storage but is never included, by its declaring module
    being the owner or by an injection, is an error: its storage could never
    be added anywhere.
  - A partial targets one generative instance (`Library.MyType`), so it cannot
    be included into another import instance by accident.
  - Inclusion is what activates hook fulfillment. A partial without storage that
    fulfills hooks still needs inclusion. A partial with neither storage nor
    hook fulfillments gains nothing from `own partial`, so that may be flagged
    as redundant.
  - **Teaching risk.** In composition, `own` contains a member *and publishes its
    names* on the container. A reader may expect `own partial` to publish the
    partial's surface into the library, which it does not. Teaching must make
    the difference clear.

- Construction order: the type's automatic members, then each partial's storage
  in partial definition order, then the constructor body, including its hook
  calls.
- Destruction order: the destructor body, including its hook calls, then each
  partial's storage in reverse definition order, then the type's members in
  reverse.
- Hooks run in partial definition order, and destruction-side hooks run in
  reverse. Definition order is source order. Injected partials precede the
  owner module's own partials, because injection runs before the target's own
  source resolves. Under `open module`, it is the application's
  module-assembly order. The order is defined, but code must not rely on it.
- **`open module` spreads the layout delay.** With storage closed at import, the
  delay stays inside the owner's module and is invisible to people using the
  type. Application closure is different in kind. Every type that embeds an
  `open module` type by value inherits the pending layout, and so does any array
  of it or generic instantiation over it. The same goes for `size of` in any
  compile-time code that touches those types. Lowering can only emit those
  layouts once the whole program is known. `open module` is not restricted, but
  teaching must warn strongly and name this consequence. The context avoids the
  delay only because the runtime constructs it and nobody embeds it by value.

#### Hooks

Hooks are an extension of `abstract`. They remain abstract because the type does
not know what it calls: zero or more fulfillments, possibly none, in which case
the calls compile away. Abstract roles gain a second way of being activated:

| Activation | Fulfilled by | Count | Call direction |
| --- | --- | --- | --- |
| `own` (current) | The immediate container | Exactly one, unless the role is optional | None: no callable hook |
| Hook point (new) | Partials | Zero or more | The type fans out to every fulfillment; no results |

```zax
MyType :: forward type

MyHookContract :: type {
  construct abstract optional : ()()
  assign abstract : ()(rhs : MyType readonly &)
}

MyType :: type seal open storage {
  hooks partial : MyHookContract
  a : Integer

  +++ final : ()() = {
    _.hooks.construct()
  }

  operator binary '=' : (
    result self : MyType &
  )(
    rhs : MyType readonly &
  ) = {
    _.a = rhs.a
    _.hooks.assign(rhs)
    return _
  }
}

// Included through an import of MyType's module.
// It fulfills the required assign and leaves the optional construct unfulfilled.
MyTagging :: partial MyType {
  hookContract own : MyHookContract
  tag : String = "none"

  onAssign fulfill hookContract.assign private final : ()(
    rhs : MyType readonly &
  ) = {
    _.tag = rhs.tag
  }
}
```

- Fulfillment names its role with `fulfill`; accidental signature matches do not
  count.
- Ordinary optionality applies with no hook-specific rule. Each partial that
  owns a contract must fulfill every non-optional role, and may fulfill each
  optional role at most once. Across all partials, a hook point has zero or more
  fulfillments. An optional `construct` role suits hooks well: each partial's
  own no-argument `+++` has already run, so the hook is an optional step after
  the main constructor has done its work.
- A hook is `bound`: `_` is the receiver, so no explicit state parameter is
  needed.
- A contract carrier holding only abstract and `final` members takes no storage.
  A contract that needs values does. Each partial's carrier is scoped to that
  partial like any other partial member.
- Hooks return nothing. Calling a role that has a result through a hook point
  is a hard error, because there is no way to combine several answers.
  Aggregating results, for example into an array, is possible future work if a
  need arises.
- A callable role's own place stance (`final` or `varying`) is open unless the
  role writes it. A fulfillment may therefore be `final` (fixed code, no storage)
  or varying (a per-instance slot, which in a partial needs `storage` and
  `callable` open). See [the `final` and `abstract` owner
  correction](#final-and-abstract-owner-correction).
- Hook bodies see only the type's non-private surface. When a partial needs
  private state, the type passes it as hook parameters, which makes the contract
  the type's deliberate interface to its partials.
- Current [composition](../../language/composition.md#abstract-roles-and-explicit-fulfillment)
  says an abstract declaration creates "no callable hook" and "no ability for
  inner code to call outward". Promotion must revise that to describe both
  activation modes.
- **A declared hook role that the type never invokes** is an acknowledgeable
  intent error. Its meaning is defined, and there are legitimate reasons for it,
  such as a contract shared by several types or calls not yet written. The
  acknowledgement encloses the hook-point declaration. That declaration is the
  positive evidence, so nothing absent has to be acknowledged. One enclosure
  covers every uninvoked role of that hook point. "Invoked" means from the type's
  own body. The category name is provisional:

  ```zax
  MyType :: type seal open storage {
    intent<uninvoked-hook-role>{
      hooks partial : MyHookContract    // assign is never invoked by MyType
    }
  }
  ```

- **Tooling recommendation only.** Tools may help a partial's author discover
  that a type's `=` or copy is hand-written and calls no assign hook, so added
  storage is not copied through it. This is quality-of-implementation advice. It
  is not a required compiler behavior or a diagnostic, and promotion must word
  it so it cannot be read as one.

#### `final` and `abstract` owner correction

This is a correction to current owners, found while checking the hook
examples.

Current [declarations and
bindings](../../language/declarations-and-bindings.md#composition-facing-member-declarations)
states that "ordinary omission in an abstract role resolves qualifier defaults
just as it does elsewhere". For callables, a declaration-side `final` resolves
the omitted type-side stance to final, so a `final` callable and a defaulted
varying callable have different storage types. Read strictly,
`start abstract : ()()` therefore asks for a varying callable. Yet every current
example fulfills such a role with `begin fulfill contract.start final : ()()`.

**Aligned resolution, corrected during promotion review.** This is not an
exception to defaulting. A role has no declaration side, so its omitted
type-side stance defaults to `varying`. A `final` fulfilling declaration is
compatible because declaration-side `final` restricts only that declaration,
just as `foo final : Foo varying` is legal. A role that writes `final` on its
type side rejects a `varying` fulfillment. (The earlier wording, "the stance is
not part of the requirement unless written", described the effect but not the
mechanism, and is superseded.) The existing general rules still apply to written stances:
`foo final : Foo varying` is legal because the declaration restricts itself,
and `foo varying : Foo final` is an error because a declaration cannot claim more
authority than its place provides.

Promotion must revise both [composition](../../language/composition.md#abstract-roles-and-explicit-fulfillment)
and declarations and bindings. It must also add one example in composition's
general abstract section, not in hook teaching, which is complicated enough:

```zax
StartContract :: type {
  start abstract : ()() final
}

MyStarter :: type {
  contract own : StartContract

  begin fulfill contract.start final : ()() = {
  }
  // `begin fulfill contract.start varying : ()() = { }` would be an error:
  // the role's type side is final.
}
```

#### `once` in partials

`once` is open by default. A partial's `once` state belongs to the partial's
module instance.

#### Execution context

- The context type is named `ExecutionContext`. Its namespace is decided only
  once every system type is known (see [deferred captures](#deferred-captures)).
- Its storage opens across modules: conceptually
  `ExecutionContext :: type seal open storage module`. Each module's additions
  are visible only where that module's context partial is granted.
- The compiler constructs every addition automatically when a thread comes
  online. Additions therefore use the no-argument partial constructor.
- A module cannot build parts it cannot name. A replacement context starts as a
  copy of the current one and changes only the parts the module can name,
  relying on the generated copy and `=`:

  ```zax
  myReplacement := currentThread.___        // generated copy carries every addition
  myReplacement.myService = myOtherService  // only the parts this module can name
  currentThread.___ = myReplacement
  ```

  A partial may declare a same-type `=` on the context. It is allowed, but it is
  ambiguous with the generated `=`. There is no guarantee it will not become
  forbidden later.

#### Enums, variants, and unions

A partial cannot add enum members, variant alternatives, or union lenses:

- **Enums and variants** promise a fixed set of values. Adding one breaks every
  existing use that relies on that set, such as an exhaustive `switch` in code
  that cannot see the partial. This changes what exists, not what is visible.
- **Unions** would be safer, but a partial must not make a safe union unsafe, and
  the use case is too narrow. Use `unsafe cast` for another view.

#### Phrases and mixfix

Phrase and mixfix additions follow the same pattern as other callables: they are
owned by the receiver, join its candidate set under grants, and use ordinary
selection. A mixfix provided by a partial that changes how `a[i] = b` breaks
down into operations is a redirect covered by the grant rules. The hazard of a
later language version reserving a phrase that a partial supplies is not treated
as a stability promise; see version locking under [deferred
captures](#deferred-captures).

#### Composition inside partials

`own`, `expose`, `via`, `preferred`, and `fulfill` in partials are expected to
follow the same rules as in the type. The difference is only which declarations
are considered, which the name-resolution and grant rules decide. Promotion must
check the legacy merge constraints against those rules instead of assuming they
hold.

### Remaining open questions

None. The maintainer confirmed on 2026-09-26 that the remaining concerns are
closed. The next step is the pre-promotion documentation fit dry run, which
requires its own assignment.

### Promotion process improvement

This is aligned for the next promotion, and it applies to promotion generally,
not only to `034`. Promotion has repeatedly drifted toward transcribing findings
rather than teaching. Rejected alternatives appear as history ("we did X, but
rejected it because Y"), and snippets appear without the question they answer.

**Diagnosis.** By promotion time, the promoting agent has spent the whole session
inside the working record. Its sense of what a reader knows is its own
knowledge. Findings are phrased as differences from the discussion, so a
finding's meaning partly depends on the alternative it replaced. An example was
clear in the working record because the discussion supplied its question, and
that discussion does not travel with it.

**Plan.** The next promotion includes changes to
[`project/documentation.md`](../documentation.md). They explain the concern
and grant judgement rather than adding rigid rules:

1. **Explain the failure mechanism.** Findings are dense, author-facing facts.
   The same finding may need a paragraph and an example in one owner and a
   single clause in another. Add two judgement tests:
   - **Counterexamples.** Would a competent programmer who never saw the
     discussion plausibly write or expect this? If so, teach against it. When a
     restriction exists for a reason, teach the reason as a consequence in the
     reader's terms, for example "if every type accepted added storage, no
     library could know any type's size". Never teach it as decision history.
   - **Examples.** Before a snippet, state the situation or question it answers.
     After it, state the consequence. If you cannot name the question the
     reader has at that point, the snippet is probably in the wrong place.
2. **Add a per-section teaching plan to the dry run.** For each owner section a
   promotion changes, record why a reader is there, what they already know at
   that point, what changes for them, and what they might wrongly try. This
   turns "where does this fact go" into "what does this reader need".
3. **Cold review from a fresh context.** After promotion, a separate agent that
   has not read the working record reads only the changed owner sections, as a
   reader arriving there directly. It reports what it cannot follow, any
   history that leaked in, and any snippet with no evident purpose.

The detail belongs in `project/documentation.md`. The operating-prompt sources
keep pointing to it unless the maintainer separately decides to mirror the
concern there. The exact wording is drafted in chat for review before that
edit. The capture wording question also belongs here: the guidance should say
that the active work file is the normal holding place for captures until the
promotion change set writes them.

### Deferred captures

These are aligned deferrals recorded here with their destinations. The raw-input
edits are written in the promotion change set.

| Concern | Destination | What must be preserved |
| --- | --- | --- |
| Generic code using partial-provided operations | Generics raw input; file to identify through [`project/raw/README.md`](../raw/README.md) | Example below; the three options; that the answer depends on where the generic becomes concrete; constraint that a caller's grant must never silently redirect a selection the library body already resolves |
| Compile-time detection of attached hooks | [Compile-time execution](../raw/compile-time-execution.md) | Example below; detection counts membership, not visibility, and is answerable only because storage and hooks close at import |
| `ExecutionContext` namespace | [Library surface and namespaces](../raw/library-surface-and-namespaces.md) | Decide only after every system type is known; candidate `Execution`; `Runtime` risks conflict with a compile-time host context; avoid `System` and `Concurrency` |
| Intent category names | [Analysis controls](../raw/analysis-controls.md) | `grant-redirected-selection`, `uninvoked-hook-role`, `application-closed-storage`, and `injected-partial-exposure` are provisional for the maintainer's collective naming review; avoid `partial-` prefixes |
| `seal` clause placement | [Cross-cutting audit](../raw/cross-cutting-audit.md), alongside word ordering after `+++` | Where the `seal` clause sits relative to other type-declaration modifiers; decide with all qualifier ordering |
| Language version locking | [Cross-cutting audit](../raw/cross-cutting-audit.md) or a new raw placeholder | Candidate **reusable language principle**, not a partial-local rule: compilers accept reasonable locking to an older language contract, lint where the language has since evolved, and avoid conflicts with code compiled against newer contracts. Phrase reservation is one instance; others already exist |
| Language- and compiler-generated partials | [Raw partial type extensions](../raw/partial-types.md) | Add design pressure only if a need for built-in conversion families arises |
| Reflection and exact diagnostic wording | [Reflection](../raw/reflection.md) and future diagnostics work | Contributions and provenance, why a partial was or was not visible at a use |
| Async context propagation | Future concurrency work, per the starting boundary | Must carry the application-closed context shape unchanged |

Generic example to preserve:

```zax
// Library module. Generic syntax is illustrative.
sum final : (result : R)(left : T, right : U) = {
  return left + right
}

// User module.
Module.MyIntegerOps :: expose partial    // adds Integer + MyType
total := sum(myInteger, myValue)
```

The options are:

- The body sees grants visible where the generic was defined. `left + right`
  finds nothing, so partial-provided operators never work through library
  generics.
- The body sees grants visible where it is called. The caller's grants flow into
  library code and can redirect other calls the library author never saw.
- A stated requirement such as "`T + U` exists" is satisfied at the call site,
  and the body uses exactly that operation without re-resolving anything else.

Compile-time hook detection example to preserve:

```zax
+++ final : ()() = {
  if compile attached _.hooks.construct {
    prepareHookState()   // pre-setup cost exists only when a hook is fulfilled
  }
  _.hooks.construct()
}
```

### Discarded or superseded

- **Order-based visibility**, including making a partial visible to all later
  source once defined, and the order-with-consistency-diagnostic option.
  Replaced by explicit grants.
- **The silent-relatching example** (`FooWide` adding `+++ : ()(rhs : Integer)`).
  It is illegal: a partial cannot add constructors. The legacy input's
  constructor relatching example is discarded for the same reason.
- **`forward` as the grant mechanism**, and the `:: partial Path` grant form.
  Replaced by `Path :: expose partial`.
- **Automatically copying partial storage around a hand-written `=`.** This
  would be hidden insertion.
- **A diagnostic on a partial that adds storage to a type with a hand-written
  `=`.** Replaced by no diagnostic plus the uninvoked-hook-role intent error.
- **Open-by-default storage.**
- **Restricting `open module` to language-provided types.**
- **Qualifying members by partial name** (`myType.MyPartial.foo`).
- **Hooks as a new declaration category separate from `abstract`.**
- **A partial disabling an original `as`.** It remains rejected by add-only.
- **Enum member, variant alternative, and union lens additions.**
- **Requiring every included partial to be defined inside the import body.**
  Replaced by `own partial` and `expose partial` references.
- **An alias as the way to include a partial.** An alias only adds a name.
- **Acknowledging an injected `expose partial` only when a redirect occurs.**
  The importer cannot see the library's code, and acknowledging in the library
  would require forking it. Replaced by an acknowledgement on the injected grant
  itself.
- **Treating `seal open module` as unacknowledged.** It now requires
  `application-closed-storage`.
- **Requiring callable roles to write their stance** to accept `final`
  fulfillments. Replaced by the open-unless-written resolution.
- **Rejected names:** `Context`, `ThreadContext`; the category words `types`,
  `type`, `schema` (already used informally across current owners), `nominal`
  (a transparent alias creates no identity), `model`, and `nesting`; and
  `partial storage` as an opening modifier.

### Owner boundaries for promotion (non-binding)

- A concept owner for `partial` itself: purpose, categories and `seal`, grants,
  name resolution, lifecycle, hooks, and closure. No current `language/` page
  owns it.
- [Type definitions](../../language/type-definitions.md): the `seal` clause,
  completion and `size of` pending state, and the no-storage character of
  `nested` additions.
- [Construction and destruction](../../language/construction-and-destruction.md):
  partial storage order, the no-argument partial constructor, and generated copy
  and `=` coverage.
- [Composition](../../language/composition.md): the hook-point activation mode
  of `abstract`, the calling error for result-bearing hook roles, and the
  `final` and `abstract` correction with its general-section example.
- [Namespaces and modules](../../language/namespaces-and-modules.md) and
  [declarations and bindings](../../language/declarations-and-bindings.md):
  `expose partial` grants and their scope, export, owner-module inclusion,
  `own partial` and `expose partial` in injection bodies, and the correction to
  how omission resolves in abstract roles.
- [Intent acknowledgements](../../language/intent-acknowledgements.md): the four
  new acknowledgeable categories (`grant-redirected-selection`,
  `uninvoked-hook-role`, `application-closed-storage`, and
  `injected-partial-exposure`), and the non-acknowledgeable
  `close storage open module` error.
- [Execution context](../../language/execution-context.md): `ExecutionContext`,
  cross-module additions, thread-start construction, and replacement by copy.
- [Integers](../../language/integers.md), [identity
  types](../../language/identity-types.md), and
  [terms](../../language/terms.md): "sealed to storage" for built-ins, and alias
  targeting.
- [Enums](../../language/enums.md), [variants](../../language/variants.md), and
  [unions](../../language/unions.md): no member, alternative, or lens additions.
- The legacy [raw partial type extensions](../raw/partial-types.md) must be
  dispositioned item by item at promotion.
- [Documentation architecture](../documentation.md): the [promotion process
  improvement](#promotion-process-improvement). This is project guidance, not
  language content, but it belongs to the same promotion.

### Initial reconstruction

The material below is the initial reconstruction written for the first review.
It is retained as evidence. Where it conflicts with the aligned findings above,
the aligned findings govern.

### Initial review entry point (superseded)

**Candidate programmer model.** A partial is a named, add-only piece of an
existing type. What it adds falls into three groups that need different rules:

| Group | Examples | Changes stored shape? | Candidate visibility rule |
| --- | --- | --- | --- |
| Surface | `final` functions, operators, phrases, `unbound` functions, nested types and aliases | No | Seen only where the partial is visible (order, `forward`, import/export) |
| Type-wide state | `once` values and `once` callables | No per-instance shape; adds global state | Names seen where visible; the state itself exists once per program or module instance (open) |
| Instance storage | Stored members, varying callable slots, union lenses, variant alternatives | Yes | Names seen where visible; the storage belongs to every instance everywhere, closed before any shape-dependent use |

The maintainer's note already implies this split without naming it. `forward`
visibility answers "which names can this use see?" Hooks and delayed `size of`
answer "what does every instance contain?" The second question cannot depend on
where a use happens to be compiled. An `Integer` or context value passed to a
library has one layout and one lifecycle, whichever partials that library can
name.

**Most important tension.** The legacy corpus says that order never selects a
surface. The note says that order and `forward` decide which partials a use
sees. These can be reconciled for surface names, because order only decides
*eligibility* and never breaks a tie. Current Zax already lets source order
decide whether a root is declared or forwarded. Even so, order-sensitive
eligibility introduces a new kind of *silent* order effect: moving a partial
can change which overload an existing, still-valid expression selects. That
effect is new, and it is the main risk (see [Order and
visibility](#order-and-visibility)). Storage cannot use order-sensitive
membership at all.

**Decisions needing maintainer review**, most consequential first:

1. Whether to adopt the surface / type-wide / instance-storage split as the
   organizing model, including graded sealing along those same lines.
2. Whether defined partials become visible to *all later source in the module*
   by order, or only through an explicit visibility grant. The note proposes
   both. See the silent-relatching example below.
3. What is open by default. If ordinary types are open to storage by default,
   every type's layout stays pending until program closure.
4. Whether lifecycle hooks are a new declaration category or an extension of
   `abstract` roles. Current `abstract` explicitly creates "no callable hook"
   and "no ability for inner code to call outward".
5. Where storage closes: at the owner's module completion (injection-fed), or at
   application closure (the context's current rule).
6. The context type's name.

**Known holes** are listed under [Holes requiring
refinement](#holes-requiring-refinement). **Captured adjacent findings** are
under [Captured and deferred](#captured-and-deferred).

### Recovered intent and evidence

- **Receiver ownership forces this feature.** Every user-defined operator
  belongs to its receiver ([operators](../../language/operators.md#discovery)).
  Without partials, `myInteger + myValue`, `intrinsicValue combines with
  customValue`, and `myInt as MyTally` have no possible owner. The note and the
  legacy input agree that this is the primary surface motivation.
- **The context is a hard storage need.** [Execution
  context](../../language/execution-context.md#one-context-shape-one-current-instance-per-thread)
  already commits to "one resolved context type shape" assembled from a core
  shape plus "permitted partial additions", fixed before runtime use, with
  every replacement having the same shape. The note adds that additions should
  be module-specific in visibility.
- **Add-only is settled in both inputs.** A partial cannot remove, hide,
  restrict, or replace original behavior. The legacy input records a weak
  pressure to let a partial disable `as` and marks it for explicit rejection.
- **Protected signatures survive.** No partial may claim a signature whose
  every operand is a closed intrinsic, or replace a protected form. `Integer +
  MyType` is not all-intrinsic, so the headline example does not collide with
  this rule.
- **Current owners already reserve the completion boundary.** [Type
  definitions](../../language/type-definitions.md#partial-and-generic-completion-boundaries)
  require that every piece able to add stored members, lenses, alternatives,
  hidden components, or lifecycle operations merge into one reproducible,
  order-independent definition *before* shape-dependent checks. That constrains
  only the instance-storage group, which is consistent with the split above.
- **Forwards and partials are already distinct.** [Declarations and
  bindings](../../language/declarations-and-bindings.md#forward-anchors) state:
  "Partial declarations add to an already completed owner; they do not complete
  a forward."
- **Intrinsics are currently sealed with a carve-out.**
  [Integers](../../language/integers.md#exact-intrinsic-family) say each
  realized specialization "is sealed against ordinary extension". The
  [terms](../../language/terms.md#sealed-type) entry adds that a separately
  authorized partial mechanism "may still permit narrowly classified additions
  without changing stored shape". That is effectively `seal storage` with an
  authorized surface opening, and it fits graded sealing well.

### Candidate model with examples

#### Surface additions

```zax
// Illustrative partial syntax from the maintainer's note.
MyIntegerOps :: partial Integer {
  operator binary '+' final : (
    result : MyResult
  )(
    rhs : MyType
  ) = {
  }
}

myResult := myInteger + myValue   // valid where MyIntegerOps is visible
```

Candidate rules:

- Candidate selection does not rank a declaration differently because it came
  from a partial. The legacy relatching example stays valid: once a
  `+++ : ()(rhs : Integer)` partial is visible, `myFoo : Foo = 100` selects it.
- Two visible partials that supply indistinguishable declarations are
  ambiguous at use, not first-wins. This matches the note's `myType.func()`
  example and the module rule that order "never breaks a name, callable, or
  operator ambiguity tie".
- A partial is named (`MyIntegerOps`). That name carries provenance for
  diagnostics, gives export/import and `forward` something to name, and is a
  candidate handle for qualification repair when two visible partials
  conflict. The repair syntax is open; `myType.MyPartialA.func()` would collide
  with ordinary member paths.

Open: does the partial's name denote anything usable as a type
(`x : MyIntegerOps`)? The candidate answer is no. It names a contribution, not
an identity.

#### Type-wide state

```zax
// Illustrative.
MyMetrics :: partial MyService {
  requestCount once : U64
}
```

The note seals this separately (`seal once`), which suggests it is its own
concern. It adds no instance shape. It does add global storage whose
initialization order, concurrency, and generative-module duplication belong to
[global and once lifetimes](../raw/global-and-once-lifetimes.md). Candidate: its
names follow surface visibility, and its storage follows the ordinary `once`
rules for the partial's own module instance. This group was not traced further
because that raw input has not been read.

#### Instance storage

```zax
// Illustrative.
MyRequestTracking :: partial Scalars.Context.ExecutionContext {   // name open
  requestId : U64
}

___.requestId = 7   // valid only where MyRequestTracking is visible
```

Candidate rules:

- Membership is not order-sensitive. Every instance of the completed type
  contains every admitted storage addition, wherever it is used.
- Visibility still applies to *names*. Module A's `requestId` exists in module
  B's contexts, but B cannot name it unless A exports that partial. This
  realizes the note's "module specific" context additions without per-module
  shapes.
- Because storage names are scoped by the partial that contributes them, two
  unrelated modules can each add `requestId` without conflict. Conflict arises
  only where both partials are visible and the name is used.
- Varying callables have per-instance slots
  ([type definitions](../../language/type-definitions.md#fixed-and-varying-callable-declarations)),
  so the note's "additional functions (varying or final)" places varying
  additions in this group, not in the surface group.

### Order and visibility

**What reconciles.** Current modules already make order decide *availability*:
"Source order affects whether a root has already been declared or forwarded
... It never breaks a name, callable, or operator ambiguity tie." Visibility is
"eligibility, not match quality". Under the candidate rule, a partial not yet
visible is simply ineligible, and two visible conflicting partials are
ambiguous. That satisfies the legacy prohibition on order-as-tiebreaker.

**What does not reconcile: silent relatching by position.** Today, moving a
declaration earlier or later makes a `forward` necessary or redundant. A mistake
produces an error, not a quiet behavior change. Order-visible partials change
that:

```zax
Foo :: type {
  +++ final : ()(rhs : U8) = {
  }
}

first : Foo = 100       // selects U8: FooWide is not yet visible

// Illustrative partial syntax.
FooWide :: partial Foo {
  +++ final : ()(rhs : Integer) = {
  }
}

second : Foo = 100      // selects Integer
```

Both lines are valid. If `FooWide` moves to an earlier file, `first` silently
changes constructor, with no diagnostic. The note justifies order sensitivity
for *compiled-and-linked* code in other libraries ("it doesn't override what an
existing version will see"). The same rule also applies inside one module,
where no such justification exists.

**Precedent pulling the other way.** The Nothing-policy directive was
deliberately made "scope-bound rather than stateful source-order mutation, so
moving an unrelated declaration or source file does not silently change later
type policies" ([namespaces and
modules](../../language/namespaces-and-modules.md#nothing-policy-defaults)).
The note's scoped `forward partial` matches that precedent. The companion rule,
"once a partial is defined it becomes part of consideration for future
compilation", is the stateful form that precedent avoided.

Candidate alternatives for the maintainer to weigh (not a recommendation yet):

- **A. Order-visible, as proposed.** Simple, and it matches separately compiled
  libraries. It accepts silent relatching within a module.
- **B. Explicit grant only.** A partial is visible only in the opening that
  declares it, nested openings, and places that explicitly grant it (scoped
  `forward partial`, or importing an exported partial). Moving code then
  produces errors, not behavior changes. It is more verbose.
- **C. Order-visible with a module-level consistency diagnostic.** The compiler
  flags a use whose selection would differ if every partial declared in the same
  module instance were visible. This keeps A's ergonomics, closes the
  intra-module hole, and still protects separately compiled consumers.

**The note's `forward` examples do not match current `forward`.** Under the
[current rules](../../language/declarations-and-bindings.md#forward-anchors):

- a qualified forward's physical placement "does not introduce" a short name,
  so `myTypeA : MyType` after `Module.C.D.MyType :: forward type` inside
  `namespace A` is already an error for a different reason;
- the forwarding source needs declaration authority over the target namespace;
  and
- a forward is source-ordered for the whole module, not scoped to the physical
  opening.

The note's `forward partial` is therefore a new thing: an opening-scoped
*visibility grant* for a contribution, not a name-and-category anchor awaiting
completion. It never completes, it does not promise a later body in the same
scope, and it may name a partial declared elsewhere. The candidate concern is
that reusing `forward` for it overloads a word whose current meaning is
"promise a later declaration". Whether the note's first example (a nesting-scoped
ordinary `forward type`) should also become an error is a separate change to
general `forward` that this item would have to trace, not assume.

### Stored members and lifecycle

**Current lifecycle rules already cover part of the problem.** Members are
constructed automatically before a constructor body, in declaration order, from
their own initializers or defaults. Destruction is automatic and runs in
reverse ([construction](../../language/construction-and-destruction.md#automatic-and-explicit-member-construction)).
Generated copy and assignment are memberwise. An added member that is
default-constructible or has an initializer therefore already has a coherent
construction and destruction story without hooks, provided added members are
ordered after original members (candidate).

**The real gap is hand-written whole-value operations.**

```zax
MyType :: type {
  a : Integer

  operator binary '=' : (
    result self : MyType &
  )(
    rhs : MyType readonly &
  ) = {
    _.a = rhs.a
    return _
  }
}

// Illustrative.
MyExtra :: partial MyType {
  tag : String = "none"
}

x = y   // copies a; x.tag is silently NOT copied
```

The same hole applies to custom copy constructors and custom `+++` replacement.
The note's hook proposal targets exactly this. The context has the same exposure
through `currentThread.___ = myReplacementContext`.

Other lifecycle holes:

- An added member with no default constructor has no source of inputs, because
  the owner's constructors cannot pass arguments to a member they cannot name.
  Candidate: storage additions must be default- or initializer-constructible,
  or be constructed by a hook.
- Initialization dependencies. A context addition whose initializer allocates
  from `___`'s default arena needs the core members constructed first. Ordering
  core-then-additions helps, but ordering among additions is unspecified.

**The hook proposal and current `abstract`.** The note reuses `abstract`, but
current [composition](../../language/composition.md#abstract-roles-and-explicit-fulfillment)
states that an abstract declaration creates "no callable hook", "no vtable",
and "no ability for inner code to call outward". The note's
`_.hooks.construct(_)` is outward fan-out from the owner to zero or more
implementations, which is the capability abstract roles deny. Candidate: a
hook point is a new declaration category (`hooks partial : MyHookContract` in
the note), and it borrows abstract roles only as the *signature contract*.
Several details need review:

- The note's partial implementations do not name their roles
  (`construct private final : ...`). Current fulfillment requires
  `fulfill hookContract.construct`, and the note's rule "accidental matches do
  not count" agrees with that. Candidate spelling:

  ```zax
  // Illustrative.
  MyPartialType :: partial MyType {
    hookContract own : MyHookContract

    onConstruct fulfill hookContract.construct private final : ()(
      state : MyType &
    ) = {
    }
  }
  ```

- The `assign` hook passes `_` (the destination). To copy added members it needs
  the source: `_.hooks.assign(rhs)`, or both.
- Because a partial is part of `MyType`, its `_` is already a `MyType`. The
  explicit `state : MyType &` parameter looks redundant unless the hook's
  receiver is the owned contract component. Which receiver a hook body has
  needs a decision.
- Every partial writing `hookContract own : MyHookContract` adds another owned
  member of the same type to one merged type. It publishes duplicate short
  paths (ambiguous under composition rules) and possibly adds storage. A
  zero-size contract and a partial-scoped path may solve this, but it needs a
  decision.
- `partial` becomes both a declaration form (`:: partial T`) and a member
  qualifier (`hooks partial : C`). That spelling overlap needs review.
- "Stubs compile away when no partial exists" requires knowing the complete
  partial set when the owner's lifecycle body is finalized. This is a
  membership question (closure), not a visibility question, so it inherits the
  storage closure rule.
- Hooks run in a defined but not-to-be-relied-on order. Candidate diagnostic
  pressure: a hook implementation that observes another partial's members
  creates the order dependency the policy forbids.

**A possible simplification to weigh.** Added storage always participates in
*generated* lifecycle automatically. Only a type that hand-writes a whole-value
operation *and* remains open to storage must provide the matching hook call.
Otherwise it is diagnosed, or it must `seal storage`. This would reduce hooks
from "every lifecycle" to "where the owner opted out of generation".

### Sealing and default openness

Graded sealing maps onto the three groups:

```zax
// Illustrative syntax from the maintainer's note.
MyType :: type seal storage callable once {
}
```

| Seal | Blocks | Group |
| --- | --- | --- |
| `seal callable` | Added functions and operators | Surface |
| `seal once` | Added `once` state | Type-wide state |
| `seal storage` | Added stored members and varying slots | Instance storage |
| `seal` | All of the above | All |

Open questions:

- Nested types and aliases have no seal category.
- `once` callables fit both `callable` and `once`.
- Enums, unions, and variants may need category-specific seals (members, lenses,
  alternatives). Current enums already "seal after their original definition".
- **Default openness.** The note's framing ("reopens a non sealed type") implies
  that unmarked types are open. Open-by-default *surface* is the usual
  extension-method tradeoff. Open-by-default *storage* means that every unmarked
  type's `size of`, layout, generated lifecycle, and union admissibility stay
  pending until closure, and every hand-written `=` gains the silent-drop hole
  above. That sits uneasily with explicit cost. Candidate: default open for
  surface, sealed for storage, with storage openness opted into by the owner
  (the context being one such owner). This is a candidate only; the maintainer
  may intend otherwise.
- **Intrinsics and the headline example.** `Integer` is a transparent,
  profile-selected alias of a concrete sealed specialization
  ([integers](../../language/integers.md#canonical-namespace-and-short-names)).
  `partial Integer` therefore extends whatever `Integer` resolves to (for
  example `I64`) for every user of that identity. On another profile, it extends
  a different type. Language-provided types also keep "one language-owned
  identity across generative imports", so a partial on them is the case where
  module-scoped visibility matters most. Also open: can a partial target a
  generic family rather than one specialization?

### `size of` and completion

- Surface-only partials never delay `size of`. Only admitted storage additions
  do, which is another argument for the group split.
- Where storage closes decides feasibility under separate compilation and
  lowering to C++. If a type's storage can grow after a library that uses it
  by value is compiled, that library cannot know its layout. Candidate closure
  points:
  - **Owner-module closure.** Only partials in the owner's module instance,
    including declarations injected through its
    [import injection body](../../language/namespaces-and-modules.md#inject-declarations-before-module-source),
    may add storage. Injection already runs "before the target's own source
    resolves", so the set is known, ordered, and reproducible when the type
    completes.
  - **Application closure.** Required for the context by current design. This
    is feasible for the context because the runtime, not library code,
    constructs context instances, and libraries reach members by path rather
    than embedding the context by value. Candidate: application-closed storage
    is permitted only for types with that property.
- The note's compile-time cycle (compile-time code needs `size of`, then uses it
  to declare more partials) is a real closure cycle. Candidate: a storage-open
  type's `size of` is unavailable to compile-time execution until closure, and a
  partial whose existence depends on that result is an error. This needs
  [compile-time execution](../raw/compile-time-execution.md), which has not been
  read.

### Execution context

- It needs a type name so that `partial` can target it. Candidate names, for
  discussion only: `ExecutionContext` (matches the owner document's title),
  `ThreadContext` (narrower, which may age badly once async propagation arrives),
  or `Context`. The namespace can be deferred, as the note proposes. Its exact
  path is left open because the `OpaqueObserver` precedent has not been read.
- Its storage additions are application-closed (see above). Their names are
  module-visible unless exported.
- Each thread's context construction constructs additions automatically.
  Additions therefore need no-argument construction or a construct hook.
- A programmer-built `myReplacementContext` must also contain every addition,
  including ones the building module cannot name. How that value is constructed
  is a hole: it needs either a whole-context copy from the current context
  followed by modification, or additions that default-construct.
- Contributions of the current [costs and
  diagnostics](../../language/execution-context.md#costs-and-diagnostics)
  section still apply, including which context member supplied a default.

### Holes requiring refinement

- Generic code: when a library generic uses `a + b` and is instantiated with a
  type from a module that has a visible partial, does the body see partials
  visible at its definition or at its instantiation? This is the most likely
  place for the note's "linked then became ambiguous" problem to reappear.
- Private access: does a partial see the owner's `private` members, and do the
  partial's own `private` members join the owner's private context? The note's
  `private` hook implementations assume some answer.
- Exporting partials: whether exporting a type exports visible partials on it,
  whether partials are exported individually, and how direct exposure
  (`:: import`) brings partial candidates in.
- Composition inside partials: added `own` members publish short paths. Added
  `fulfill` declarations and `preferred`, `via`, and `forbidden family`
  interact with the merge constraints already listed in the legacy input.
  Under order-sensitive visibility, a published path would be visible only
  where its partial is.
- Diagnostics must name the original owner and every contributing partial,
  including why a partial was or was not visible at a use.

### Captured and deferred

- **Async context propagation.** Excluded by the starting boundary. Constraint:
  any propagation must carry the application-closed context shape unchanged.
- **Phrase and mixfix additions.** Include the legacy adopted-versus-reserved
  phrase hazard and precedence repricing. These are surface additions, so they
  follow surface visibility. They can wait until the surface model is aligned.
  Owner: [operator phrases](../../language/operator-phrases.md) and the legacy
  raw input.
- **Language- and compiler-generated partials** for intrinsic conversion
  families (legacy provenance classes). They may reuse the model with
  privileged authority. They can wait. Constraint: the model must not depend on
  source order for language-owned additions.
- **Enum member additions.** The legacy input lists exhaustiveness, implicit
  values, flags masks, and string conversion. Current enums seal after
  definition. They can stay sealed until pressure arises.
- **Reflection** of contributions and provenance: [reflection raw
  input](../raw/reflection.md), which has not been read.
- **`once` initialization and teardown order** for type-wide state: [global and
  once lifetimes](../raw/global-and-once-lifetimes.md), which has not been read.
- **Disabling an original `as`** stays rejected under add-only; it only needs
  recording in the final rejection list.

### Likely owner boundaries (non-binding)

- A new or existing concept owner for `partial` itself, covering its purpose,
  groups, visibility, sealing, conflicts, and hooks. No current `language/` page
  owns it. The legacy root `partial.md` page is referenced by the raw input's
  provenance but was not read.
- [Type definitions](../../language/type-definitions.md): completion boundary,
  seal qualifiers on type declarations, and `size of` pending state.
- [Construction and destruction](../../language/construction-and-destruction.md):
  added-member order and hook participation in hand-written lifecycle.
- [Composition](../../language/composition.md): the relationship between hook
  points and `abstract`, and merge rules.
- [Namespaces and modules](../../language/namespaces-and-modules.md) and
  [declarations and bindings](../../language/declarations-and-bindings.md):
  visibility grants and any change to `forward`.
- [Execution context](../../language/execution-context.md): the context type
  name and addition rules.
- [Integers](../../language/integers.md), [identity
  types](../../language/identity-types.md), and
  [terms](../../language/terms.md): the sealed-intrinsic carve-out.

### Reading performed

Beyond required reading: specific sections of composition (abstract roles),
construction (automatic member construction and generated copy and assignment),
integers (exact intrinsic family and canonical names), and the terms entry for
sealed types. Each was triggered by a concrete consequence named above. The
consequence-driven operator, candidate-selection, casting, identity, enum,
structural-shape, and raw inputs have not been read yet.

## Dispositions and promotion dry run

### Result: PASS, with `project/documentation.md` held

**Revision, 2026-09-26.** The maintainer resolved the failing items:

- **Fences govern only the type's own surface.** A type's exposure fences do
  not reach a partial's declarations; `seal close callable` is the whole-type
  control.
- **Published names versus partial declarations** is confirmed as an ordinary
  ambiguity outside the type.
- **Replacement is revised.** During every replacement of the type, including
  the generated fallback, each partial's storage uses the partial's own
  no-argument `+++ replacement` if it declares one, called automatically.
  Otherwise it receives `---` followed by its no-argument `+++`. A partial
  replacement with parameters remains an error; a partial that needs the
  incoming values uses a hook called from the type's custom replacement. This
  supersedes "`+++` replacement in a partial is an error" in the aligned
  constructor findings and the carried-forward derivation below.

The documentation guidance wording was subsequently approved and applied; see
the promotion record.

The original failure record follows.

The owner structure, reading path, deferral destinations, and change set below
are coherent. The dry run fails on two items that require alignment before
promotion can begin:

1. **Composition fences and partial additions.** An owner's
   `= forbidden family` fence currently blocks "every generated, exposed,
   adopted, or later direct signature in that outer family". No aligned finding
   says whether a type's fence also prevents a partial from adding a
   declaration with that name. There are two readings:
   - **Fences govern only the type's own surface.** A partial's declarations
     belong to the partial's scope, so they are not fenced. This is consistent
     with the aligned rule that the type and its partials are distinct scopes,
     and `seal close callable` remains the whole-type control.
   - **Fences also bind partials.** This gives an owner a per-name tool between
     sealing nothing and sealing all callables.

   Agent lean: the first reading.
2. **Documentation guidance wording.** The aligned [promotion process
   improvement](#promotion-process-improvement) says the exact wording for
   `project/documentation.md` is drafted in chat for review before that edit.
   The draft is recorded below.

Two consequences are derived from aligned rules and existing owner behavior
rather than newly decided. They are promoted as stated unless the maintainer
objects:

- **Published paths versus partial declarations.** Composition's precedence,
  under which a direct declaration owns its name over an `own`-published short
  path, operates within one scope. Outside, a type's published `label` and a
  granted partial's `label` are the type's surface and the partial's surface.
  Their combination is therefore an ordinary ambiguity, not "the direct
  declaration wins".
- **Replacement.** The generated replacement fallback runs the type's ordinary
  `---` and `+++`, so partial storage is destroyed and default-constructed
  again as part of them. A custom `+++ replacement` on the type cannot name
  partial storage. Members start live inside a replacement constructor, so
  partial storage is carried forward unchanged unless the body calls a hook.

### Ownership map

| Aligned finding | Lasting owner |
| --- | --- |
| Purpose, mental model, grants, name resolution, `seal` categories and defaults, storage inclusion and lifecycle order, hooks, `once`, `open module`, enums/variants/unions, costs, diagnostics, source stability | New [`language/partials.md`](../../language/partials.md) (concept owner) |
| `seal` clause as part of a type declaration; completion and `size of` pending state | [Type definitions](../../language/type-definitions.md), with a handoff to partials |
| Partial storage in construction, destruction, generated copy and `=`, and replacement | [Construction and destruction](../../language/construction-and-destruction.md), with local rules and a handoff |
| Hook-point activation of `abstract`; result-bearing hook calls; `final` and `abstract` correction and its general example; partial scopes and composition precedence | [Composition](../../language/composition.md) |
| Grants as declarations; omission in abstract roles; partial declarations and forwards | [Declarations and bindings](../../language/declarations-and-bindings.md) |
| Injection-body `own partial` and `expose partial`; owner-module inclusion; export of partials | [Namespaces and modules](../../language/namespaces-and-modules.md), with a handoff to partials |
| Four new acknowledgeable categories and the non-acknowledgeable `close storage open module` error | [Intent acknowledgements](../../language/intent-acknowledgements.md) registry and entries |
| `ExecutionContext`, cross-module additions, thread-start construction, replacement by copy | [Execution context](../../language/execution-context.md) |
| Built-ins sealed to storage, open to callables; alias targeting | [Integers](../../language/integers.md), [identity types](../../language/identity-types.md), [terms](../../language/terms.md) |
| Receiver extension through partials | [Operators](../../language/operators.md), [operator phrases](../../language/operator-phrases.md), [mixfix operators](../../language/mixfix-operators.md), [conversions and casts](../../language/casting.md) |
| Partial candidates and literal preference | [Integer literals](../../language/integer-literals.md) source-stability entry |
| No enum member additions | [Enums](../../language/enums.md) boundaries |
| Shape change from storage partials | [Structural shapes](../../language/structural-shapes-and-compatibility.md) boundaries |
| Terms: partial, grant, hook point, sealed type | [Terms](../../language/terms.md) |
| Promotion process improvement | [Documentation architecture](../documentation.md) |

### Affected files and exact change set

**New current owner**

- `language/partials.md`. Status: current conceptual design. Teaching plan
  below.

**Current owners updated**

- `language/type-definitions.md`: rewrite "Partial and generic completion
  boundaries" into current behavior, and add a short `seal` clause handoff
  under completion. Replace the partial-type item in the maturity footer.
- `language/construction-and-destruction.md`: add a short section on partial
  storage (automatic no-argument `+++` and `---`, order, generated copy and `=`
  coverage, hand-written operations and hooks, replacement consequences), with a
  handoff to partials.
- `language/composition.md`:
  - revise "Abstract roles and explicit fulfillment" to state the two
    activation modes;
  - add the stance-open-unless-written rule and the written-`final` example to
    the general abstract section;
  - add partial-scope handling of published paths and, per the dry-run
    resolution, fences;
  - update the boundaries entry about partial authority over fences.
- `language/declarations-and-bindings.md`: correct "ordinary omission in an
  abstract role resolves qualifier defaults" for callable stance; add
  `expose partial` to declaration forms; update the maturity item "future
  generic and partial work".
- `language/namespaces-and-modules.md`: replace "Future partial-type or extension
  work may define another explicit authority mechanism" with the current
  partial routes; add an injection subsection for `own partial` and
  `expose partial`; update metadata and maturity lists.
- `language/intent-acknowledgements.md`: add registry rows and entries for
  `grant-redirected-selection`, `uninvoked-hook-role`,
  `application-closed-storage`, and `injected-partial-exposure`; add
  `seal close storage open module` as a non-acknowledgeable example if the
  owner's list pattern calls for it.
- `language/execution-context.md`: name `ExecutionContext`, state
  cross-module additions, thread-start construction, and replacement by copy;
  update metadata and the deferred list.
- `language/integers.md`: correct "sealed against ordinary extension" to
  sealed storage with open callables; replace the future-partial paragraph and
  the maturity item.
- `language/identity-types.md`: replace "Partial definitions" and the "closing
  brace ... seals" wording with current behavior and a handoff; update metadata
  and maturity.
- `language/terms.md`: rewrite "Sealed type"; add "Partial", "Visibility
  grant", and "Hook point" entries.
- `language/operators.md`: replace the future-partial paragraph with the current
  route; keep the provenance-supplies-no-preference sentence.
- `language/operator-phrases.md`: replace the future-partial paragraph so the
  intrinsic-first phrase becomes available through a granted partial.
- `language/mixfix-operators.md`: replace the future-partial sentence and the
  maturity item.
- `language/casting.md`: in "Your types and the language's scalars", add the
  partial route for `myCount as MyTally`.
- `language/integer-literals.md`: reword the source-stability item as granting
  a partial.
- `language/enums.md`: replace "partial or open enum extension" in the deferred
  list with the current rule; `Does Not Own` updated.
- `language/structural-shapes-and-compatibility.md`: update metadata and the
  deferred item to state that storage partials change shape before completion.
- `language/unions.md` and `language/variants.md`: one boundary sentence each,
  only where the owner already lists extension or completion boundaries.
- `index.md`: add partials to "Start here" and "Current conceptual design";
  remove the legacy link.

**Legacy and raw retired**

- `partial.md` (root legacy page): delete. Every useful claim is promoted or
  superseded: constructed in import or declaration order; no reliance on other
  partials; added storage; name conflicts; no standalone instance; memory and
  compatibility cost. The claims about intercepting `final` functions and
  construction are superseded by name scoping and hooks.
- `project/raw/partial-types.md`: delete after the item-by-item disposition
  below.
- `project/raw/maintainer-notes/partial.md`: delete; consumed by this item.
  The `size of` and compile-time cycle pressure moves to the compile-time raw
  input.
- `project/raw/README.md`: remove both rows.
- `project/raw/feature-catalog.md`: route "Partial types" to the new owner.

**Raw captures written**

- `project/raw/type-parameters-and-generics.md`: generic code using
  partial-provided operations, including the example, options, and constraint;
  and language- or compiler-generated partials for protected numeric conversion
  families, in "Generic protected numeric conversion".
- `project/raw/compile-time-execution.md`: `compile attached` hook detection,
  and `size of` for storage-open types before closure, including the
  compile-time cycle in which a partial's existence depends on that size.
- `project/raw/library-surface-and-namespaces.md`: `ExecutionContext` placement.
- `project/raw/analysis-controls.md`: the maintainer's collective naming review
  of the four provisional intent categories.
- `project/raw/cross-cutting-audit.md`: two new entries. The first is language
  contract locking for syntax and reservation evolution, a candidate reusable
  principle whose likely owner is safety and analysis "Language contracts and
  compiler analysis", which already owns contract selection for analysis. The
  second is `seal` clause placement, added to the existing word-order entry.
- `project/raw/cpu-provider-model.md`: CPU-provider-supplied `final` functions
  on built-ins, as pressure only if a need arises.
- `project/raw/global-and-once-lifetimes.md`: a constraint that `once` state
  added by a partial belongs to the partial's module instance.

**Project guidance**

- `project/documentation.md`: the aligned promotion process improvement, using
  the wording below after chat review.

**Not changed**

- `project/README.md` current-work index: closure is separate.
- Operating-prompt sources.

### Legacy raw disposition: `project/raw/partial-types.md`

| Section | Disposition |
| --- | --- |
| Mixfix ownership pressure | Promoted: mixfix additions follow the callable pattern |
| Phrase extension pressure | Promoted: an intrinsic-first phrase becomes available through a granted partial on the intrinsic |
| Conversion pressure | Promoted: a partial on a scalar may add `as`; disabling an original `as` rejected by add-only |
| Universal receiver ownership decision list | Promoted: authority (seal and grants), conflicts (ambiguity), reproducibility (grants and export), visibility (grants), aliases (resolved identity), generative identity (instance-bound), diagnostics (deferred) |
| Protected signatures | Promoted: the existing operators rule applies to partials |
| Adopted-versus-reserved phrase conflict | Captured in the cross-cutting audit (contract locking) |
| Intrinsic and identity provenance classes | Promoted for owner and programmer partials; language-, compiler-, and CPU-provider partials captured in raw inputs |
| Stored members and identity shape | Promoted: shape is final only after storage closes |
| Completion boundary | Promoted into type definitions |
| Composition-aware merge conflicts | Promoted under partial scopes, pending dry-run item 1 |
| Candidate relatching | Constructors discarded; other surface promoted as granting a partial |
| Execution-context shape questions | Promoted |
| Future-work decision list | Each answered above or captured |
| Enum extension questions | Rejected: no enum member additions |

### Structure proposal

- One new owner, `language/partials.md`, beside the other concept owners. No
  directory changes.
- `index.md` routes readers to it, and the legacy link is removed.
- Other owners keep their local rules and hand off to partials for the
  complete model.
- Raw inputs keep their deferred material, reachable from `project/raw/README.md`
  and from no current owner.

### Teaching plans

#### `language/partials.md`

Readers arrive wanting to add something to a type they do not own, most often
`myInteger + myValue`, or wanting to add per-thread state to the execution
context. They know types, receiver-owned operators, modules, and `abstract`
roles. Sections, in order:

1. **Why partials exist.** Lead with `myInteger + myValue` failing and a
   partial on `Integer` fixing it, followed immediately by the grant that makes
   it usable. Mental model: a partial adds to a type, and nothing changes
   anywhere until a scope asks for it.
2. **Making a partial visible.** Cover the grant form, forward scope within a
   block, export, partial-to-partial grants, and a type granting its own
   partial. Likely wrong expectation: a partial is visible once declared. Then
   the mid-block redirect acknowledgement.
3. **Names inside and outside.** Cover the three vantage points, same-named
   members as distinct declarations, ambiguity and narrowing the grant as the
   repair, hiding inside the partial and why no `shadowable` is required, no
   private access, and a partial's name not being a type. Likely wrong
   expectations: qualifying with the partial's name, and the type seeing its
   partial.
4. **What a type allows.** Cover the `seal` clause and categories table,
   defaults, and why storage is closed by default, taught as a consequence ("if
   every type accepted storage, no library could know a type's size"). Then
   compound additions, built-ins, alias targeting, and enums, variants, and
   unions, with the reason for each in reader terms.
5. **Adding storage.** Cover owner-module inclusion; the three injection forms,
   with `own partial` recommended and `expose partial` acknowledged; the
   automatic constructor and destructor; order; and generated versus
   hand-written copy. Likely wrong expectations: a partial adding a
   constructor, and hand-written `=` copying added storage.
6. **Hooks.** Cover contract, hook point, `fulfill`, optional roles, calls
   compiling away, no results, private state passed as parameters, and the
   uninvoked-role acknowledgement. A tooling note is phrased as advice.
7. **`once` in partials.**
8. **Opening storage across modules.** Cover `seal open storage module`, the
   spreading delay taught through a concrete embedding example, the
   acknowledgement, and the execution context as the motivating case.
9. **Costs and diagnostics**, **source stability**, and **boundaries and
   maturity**.

#### Local owner sections

- **Type definitions, completion.** A reader checks when a type's layout is
  final. Add one example of a `seal` clause and when `size of` becomes
  available, then hand off.
- **Construction, partial storage.** A reader writes a constructor or `=` for an
  extensible type. Teach order, generated coverage, and the hand-written hook
  obligation with one example.
- **Composition, abstract roles.** A reader learning `abstract` meets a second
  activation mode. Present the two modes as a table after the existing `own`
  teaching; the stance rule and its example belong to the general part, not to
  hooks.
- **Namespaces, injection.** A reader injecting into an import learns that it
  can include partials. Show `own partial` first, then `expose partial` with
  its consequence.
- **Operators, phrases, mixfix, casting.** A reader told "no receiver can own
  this" now needs the route. Replace the future-work paragraph with a short
  example and a handoff.
- **Integers, identity, terms, enums.** Correct the statements with brief
  reasons.
- **Execution context.** A reader wants per-thread state. Show a partial on
  `ExecutionContext` and replacement by copy.

### Validation plan

- Live-link and anchor check across the changed files.
- A fresh-context review of the changed owner sections by an agent that has not
  read this working record, as aligned in the process improvement.
- Search for surviving "future partial" wording in live owners.

### Draft wording for `project/documentation.md`

Under "Human-developer-facing depth", after "Positive-first promoted teaching":

> ### Promotion re-authors for a different reader
>
> A working record is written for its authors. Its findings are dense, and their
> meaning often depends on the discussion that produced them. A finding may be
> phrased as the alternative that survived, and an example may have been clear
> only because the surrounding exchange supplied its question. By promotion
> time, the promoting agent has usually spent a long session inside that record,
> so its sense of what a reader already knows is its own.
>
> Left unchecked, that vantage produces two recurring failures:
>
> - **Decision history presented as teaching.** "We previously did X but
>   rejected it because Y" teaches the reader about something they never
>   learned.
> - **Transplanted snippets.** An example moved without the situation it answers
>   can confuse more than it clarifies.
>
> The same finding can need very different treatment in different owners: a
> paragraph and an example where a reader first meets the concept, a single
> clause where it only bounds a local rule. Decide per destination, from the
> position of a reader who arrived at that section directly. Two judgement
> tests help:
>
> - **Would a competent programmer who never saw the discussion plausibly write
>   or expect this?** If so, teach against it. When a restriction exists for a
>   reason, teach the reason as a consequence in the reader's terms, for
>   example "if every type accepted added storage, no library could know any
>   type's size", rather than as the decision that was taken.
> - **Can you name the question this example answers for the reader at this
>   point?** State that situation before the snippet and the consequence after
>   it. If you cannot name the question, the example probably belongs elsewhere
>   or not at all.
>
> These are judgement aids, not a template.

In "Pre-promotion documentation fit dry run", after step 9:

> 10. Records a short teaching plan for each owner section the promotion
>     changes: why a reader is there, what they already know at that point,
>     what changes for them, and what they might wrongly try.

In "Validation":

> - After a substantial promotion, an agent that has not read the working record
>   reviews the changed owner sections as a reader arriving directly. It reports
>   what it cannot follow, leaked discovery history, and examples without an
>   evident purpose. The promoting agent's own cold-reader check is not a
>   substitute, because it shares the working context.

In "Consequences and deferrals", after the capture paragraph:

> Until promotion, the active work file is the normal holding place for a
> capture: record the finding, its destination, and what the destination must
> preserve. The promotion change set writes it into the raw input or owner.

### Promotion record

Promotion was applied on 2026-09-26 under the maintainer's authorization to
promote upon PASS. It differs from the change set above in these ways:

- **Raw retirement is deferred to archival.** `project/raw/partial-types.md` and
  `project/raw/maintainer-notes/partial.md` remain until this item is archived,
  because this file's fixed initiating input links to them. Their
  `project/raw/README.md` rows now say they are consumed and retire at archival.
  The root `partial.md` page was deleted.
- **A partial `as` on scalars is not promoted as usable.** The fresh-context
  review showed that the only current `as` declaration form uses an unrestricted
  type-argument slot, so a partial `as` on `I32` would also compete with
  protected conversions such as `myCount as I64`. [Conversions and
  casts](../../language/casting.md#your-types-and-the-languages-scalars) now
  says the route waits on type-argument restriction, and the pressure is
  recorded in the generics raw input's "Restricting which types a slot
  accepts". This supersedes the "Conversion pressure" row of the legacy
  disposition table.
- **Unions and variants are unchanged.** Neither owner lists extension
  boundaries, so the rule lives only in the partials owner.
- `project/documentation.md` was updated after the maintainer approved the
  drafted wording unchanged.

**Fresh-context review.** A separate agent that had not read this working record
reviewed the new owner and every changed section. Its findings were fixed:

- a construction example contradicting the stated construction order;
- the scalar `as` example above;
- a stale error comment in operator phrases;
- missing explanation of how a partial joins a hook point;
- whether a grant inside `intent<...>{ }` still covers the rest of the block;
- a context example without an initializer;
- an unexplained reset of partial storage on replacement;
- identity-types wording that still used "seals" in its old sense;
- `=` examples missing `final`;
- an over-narrow fulfillment count for relaxed roles;
- unlinked prerequisite terms;
- a hook comment that did not match its example;
- a weak reason for why a partial cannot reach a hidden original;
- future-work text placed inside teaching; and
- "replace" wording in mixfix that conflicted with add-only.

**Derived statements, confirmed by the maintainer on 2026-09-26:**

- `uninvoked-hook-role` applies to optional and required roles alike; and
- a type that embeds `ExecutionContext` by value inherits its pending layout.

**Hook linkage revised, aligned 2026-09-26.** The first promotion linked a
partial to a hook point by the partial owning a member of the same contract
type (`hookContract own : MyCatalogHooks`), following the maintainer's original
note. That linkage cannot distinguish two hook points with the same contract.
It is replaced by the existing path-based fulfillment identity:

- a partial fulfills a hook role by naming the type's hook-point path, for
  example `fulfill hooks.assign`, and declares no carrier;
- several hook points with one contract are therefore fulfilled separately by
  name;
- a partial that fulfills any role of a hook point must fulfill every required
  role of that hook point and at most once each optional role; a partial naming
  none of its roles does not take part;
- a hook point meant for partials cannot be `private`, because a partial can
  name only what it can see; and
- a hook-point contract cannot contain value roles, for the same reason a
  result-bearing role cannot be called through it.

This supersedes the aligned statements that each partial's contract carrier is
scoped to that partial and that a contract needing values takes storage. The
partials and composition owners were updated accordingly.

**Maintainer review of promoted documentation, 2026-09-26.** Aligned and
applied:

- **Casting.** The scalar-`as` passage is rewritten plainly: a partial cannot
  yet solve `myCount as MyTally`, because an unrestricted `as` on `I32` would
  also claim `myCount as I64`.
- **Hook-call wording.** "None means no call" and "compiles away" are replaced
  by "if no partial fulfills a role, the compiler removes the call".
- **Stance mechanism** is corrected, as recorded in the owner-correction
  finding; the composition subsection is renamed "`final` fulfillment of a
  callable role".
- **Context `=`.** A partial-declared same-type `=` on `ExecutionContext` is
  legal but not recommended.
- **Documentation guidance.** Work-record findings must be understandable to a
  reviewer, and density is never a goal.
- **Raw bodies do not cite numbered work.** A raw input explains each finding in
  place and cites numbered work only in `Source / Provenance`, so readers are
  not invited into archived work whose conclusions may have been revised.
  `project/documentation.md` now states this, replacing its earlier permission.
  Body references were removed from every raw input, including older ones in
  `analysis-controls`, `safety`, `cross-cutting-audit`, and
  `mutability-indexed-type-families`, with provenance moved to metadata. Two
  quoted examples inside the cross-cutting audit's entry about this pattern
  remain.
- **Partials teaching.** The grant example now shows the `MyPointOps` partial
  that supplies `normalizeTo`. Export moved out of the first visibility section
  into a later "Partials from another module" subsection with an import
  example.
- **`forward partial`.** `Module.A.B.MyPartial :: forward partial` anchors a
  partial name for forward reference and completes through exactly one partial
  declaration or exact `alias partial`. Grants, injection references, and
  aliases may name it before completion, with dependent checks pending. It has
  nothing to do with injection or visibility.
- **`alias partial`.** An alias preserves the partial's identity and works
  wherever a partial path does. It adds a name only; it neither includes nor
  grants.
- **Compatibility.** Adding storage changes shape, so structural compatibility
  that depended on the old shape can stop holding. This is expected, explained,
  and not prevented.
- **Redundant inclusion.** `own partial` naming a partial that is already
  included, whether declared in the owner module or defined in the same
  injection body, is legal, has no effect, and may be flagged by tooling.
  `expose partial` in an injection body is never redundant in this way, because
  it also grants visibility to the library's source.

**Second fresh-context review.** A new agent without this record reviewed the
revised sections. Fixed:

- `writable` receivers added to hand-written `=`, the `assign` role, and its
  fulfillments;
- `ExecutionContext` named as the exception to built-ins being closed to
  storage;
- `forward partial` completion by declaration or exact alias stated
  consistently;
- the mid-block grant example now declares `MyFoo` and `FooWide` and explains
  why `100` prefers `Integer`;
- the published-`label` case is explained in place;
- the `seal` clause meaning is stated precisely: it starts from the defaults,
  each `open` or `close` applies to the categories after it, and bare `seal`
  closes all;
- the reverse order of hooks called from `---` is defined;
- construct-hook timing is corrected;
- the partial-to-partial example no longer uses an undeclared `format`;
- the cross-module example path now starts at the import name;
- reasons are given for the absence of qualification by partial name and of a
  path to a hidden original;
- the casting passage no longer narrates progress;
- a current rule was removed from a structural future-work list; and
- the declarations rule now allows a partial on a type still pending behind
  `forward module`, as aligned.

Retained as the maintainer directed: the "not guaranteed to remain permitted"
caveat on the context's `=`, and qualified `forward partial` paths. The
"reconciled with" provenance wording matches other owners.

**Closure.** The maintainer reviewed the promoted documentation and authorized
closure on 2026-09-26. No provisional teaching-debt observation remains. The
consumed raw inputs `project/raw/partial-types.md` and
`project/raw/maintainer-notes/partial.md` were retired in the closing change
set. Links from this record to them are historical and are resolved through Git
history. The fixed input, reading scope, and initial reconstruction are
retained unchanged as evidence.

**Validation.** Live-link and anchor checks pass across the live tree. No live
owner retains "future partial" wording.
