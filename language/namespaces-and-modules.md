# Zax namespaces and modules

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers organizing declarations or using code across Zax modules |
| Applies To | Programmer-facing namespaces, module roots, source order, imports, injection, declaration exposure, visibility baseline, and module identity; not a formal grammar or build specification |
| Implementation State | Not established by this repository |
| Owns | Protected module-local roots; language-provided root declarations; namespace declaration, reopening, placement, ownership, and self-name shadow permission; source-file contribution and order; named and direct imports; generative module instances; import injection and exact shared dependencies; direct-versus-imported collision behavior; module-internal default visibility; explicit export and non-transitive import baseline; `export` directive attachment and ranges; directive state stopping at imports; module cycles; namespace/module diagnostics, costs, and source stability |
| Does Not Own | General lexical declaration lookup, aliases, and forwards ([declarations and bindings](declarations-and-bindings.md)); type-alias identity ([identity types](identity-types.md)); general directive attachment and ranges ([compiler directives](compiler-directives.md)); exact `private` and selective-export options; package acquisition and locking; complete global/module initialization; compile-time queries ([compile-time code](compile-time-code.md)); partial behavior ([partials](partials.md)); reflection; or implementation architecture |

## Start with a named import

A named import gives another module one stable local root:

```zax
Geometry :: import Module.MyGeometryModule

point : Geometry.Point
distance := Geometry.distance(point, origin)
```

`Geometry` is the importing source's name for this imported module instance.
Declarations exported by that instance remain below the root. A subnamespace
keeps the same relationship:

```zax
shape : Geometry.Shapes.Polygon
```

This qualification is more than style. It:

- shows which import owns the declaration;
- avoids collisions with unrelated modules;
- allows several versions or configurations to coexist;
- remains stable when the imported module adds another export; and
- gives diagnostics and tools a durable route to the declaration.

Imports are **generative**. Importing the same source twice creates two module
instances and two sets of declarations:

```zax
GeometryA :: import Module.MyGeometryModule
GeometryB :: import Module.MyGeometryModule

first : GeometryA.Point
second : GeometryB.Point = first
// error: GeometryA.Point and GeometryB.Point have distinct identities
```

Equal source, layout, or injected text does not merge those identities.
[`compatible` posture](structural-shapes-and-compatibility.md#compatibility-posture)
may permit an explicit structural relationship, but it never makes two
declarations identical.

## Every module has its own root

Every application and imported module instance has one unnamed declaration
root. `Module` is the protected name for the current instance's root:

```zax
value : Module.Geometry.Point
```

Inside imported source, `Module` means that imported module's root, not the
importer's root. The importer's local name does not rewrite it:

```zax
ThirdParty :: import Module.ThirdPartyDefinition
```

Source inside `ThirdParty` still uses its own `Module`. There is no
process-wide absolute namespace path.

`Module` cannot be redeclared, shadowed, replaced by an import, or retargeted by
an alias.

### Language-provided declarations

Declarations that are part of Zax itself are present automatically in every
module root. An independently developed library is not a built-in and still
requires import.

`Scalars` is a protected exact alias to one language-owned namespace:

```zax
Scalars.Integers.I32
Scalars.Fixed.I16F8
Scalars.Floating.Binary32
```

The same paths are available explicitly through the current root:

```zax
Module.Scalars.Integers.I32
```

Short built-in names such as `Integer`, `I32`, and `Boolean` are protected
language-provided aliases. They do not create another identity and cannot be
shadowed or replaced.

Each generative module instance receives aliases to the same language-owned
declarations:

```zax
first : GeometryA.Scalars.Integers.I32
second : GeometryB.Scalars.Integers.I32 = first
// Legal: both paths denote the language-owned I32 identity.
```

This sharing is specific to exact language-provided declarations. It does not
merge declarations written by `GeometryA` and `GeometryB`.

`Compilation` is another protected language-provided name. It answers
compile-time questions about the build, such as `Compilation.module.location`,
and its answers are specific to the module instance asking. See
[compile-time code](compile-time-code.md#compilation-information).

## Namespaces organize one module

A namespace is a named declaration scope under the module root or another
namespace:

```zax
namespace Geometry {
  Point :: type {
    x : Float
    y : Float
  }
}
```

Namespaces use type-like capitalization and ASCII identifier spelling.

Namespace declarations are legal only:

- at module root; or
- directly inside another namespace.

They are not legal inside functions, ordinary blocks, or type bodies. A
namespace is a module declaration scope, not a named local lifetime or an
implicit type-owned storage mechanism.

### Qualified declaration and reopening

Nested and qualified declarations can contribute to the same namespace:

```zax
namespace Geometry {
  namespace Shapes {
    Circle :: type {
    }
  }
}

namespace Geometry.Shapes {
  Polygon :: type {
  }
}
```

For `namespace Geometry.Shapes`:

1. `Geometry` resolves as an ordinary root at that source position.
2. Every intermediate component must already be a namespace or forwarded
   namespace.
3. Only the final component may be introduced or reopened.

Use `Module` when the module-root path must be explicit:

```zax
namespace Module.Geometry.Shapes {
}
```

Reopening contributes to one namespace identity. It is not same-scope
redeclaration.

### Who may reopen a namespace

Ordinary source may reopen a namespace owned by its own module instance. Source
cannot extend an imported namespace merely because it can name it:

```zax
Library :: import Module.LibraryDefinition

namespace Library.Utilities {
  // error: this module instance does not own Library.Utilities
}
```

An import-injection body is different. Its declarations become source of the
target module instance, so it may contribute to a target-owned namespace:

```zax
Library :: import Module.LibraryDefinition {
  namespace Utilities {
    // Target-module contribution.
  }
}
```

An exact namespace alias preserves identity but does not transfer reopening
authority.

Adding to an imported *type* is a different mechanism. A
[partial](partials.md) may add functions to a type it cannot reopen, and becomes
visible only where a scope grants it. Stored members added by a partial reach an
imported type only through an injection body, described below.

### Self-name shadow permission belongs to one opening

A namespace opening may permit its body to introduce a declaration that hides
the namespace's own unqualified name:

```zax
namespace Utilities shadowable {
  Utilities :: forward type
  // Legal: this opening introduces an incomplete same-named member.
}

namespace Utilities {
  Utilities :: type {
  }
  // Legal completion of the existing forwarded member.
}
```

Only the opening that introduces the shadowing declaration or forward needs
`shadowable`. The second opening completes the existing member and does not
repeat the permission.

This modifier is local to the opening:

- it is not a property of the namespace identity;
- later reopenings do not inherit a general shadow grant;
- it does not permit hiding unrelated outer names; and
- it cannot be applied to a protected namespace.

After the nested member exists, `Utilities` within later namespace source may
name that member. `Module.Utilities` remains the explicit route to the
namespace itself.

### Nothing-policy defaults

The compiler directive:

```zax
[<nothing-instance-default=trap>]
```

selects the omission default for types declared in the attached module or
physical namespace opening. `default` selects compiler-prepared readable
Nothing behavior.

A namespace setting is local to one opening rather than stored on the namespace
identity:

```zax
[<nothing-instance-default=trap>]
namespace Services {
  First :: type {
  }
}

[<nothing-instance-default=default>]
namespace Services {
  Second :: type {
  }
}
```

`First` inherits `trap`; `Second` inherits `default`. The declarations still
belong to the same `Services` namespace identity.

A nested namespace opening inherits from its physical containing opening and
restores the outer setting when it ends. Reopening a namespace elsewhere does
not inherit an earlier opening's local directive. Conflicting settings attached
to one opening are errors; compatible repetition is redundant.

A module-level setting is one property of that generative module instance and
is inherited by its namespace openings unless they override it. Conflicting
module-level settings are errors. An explicit `+++ final once` declaration on a
type always overrides the inherited omission default.

This attachment is scope-bound rather than stateful source-order mutation, so
moving an unrelated declaration or source file does not silently change later
type policies. Complete Nothing behavior belongs to
[Zax Nothing instances](nothing-instances.md#set-a-scoped-default).

Language-provided built-in types are not declared inside the opening and do not
inherit this directive. Their Nothing policy is canonically trapping across
modules, so one built-in pointer identity never changes vacancy meaning
according to its lexical use site.

## Files contribute to one ordered module

Every module has a `module.zax` source that establishes its source-file set and
source-file order. All selected files contribute declarations to the same
module root. A file is neither a namespace nor an identity layer.

Source order affects:

- whether a root has already been declared or forwarded;
- when an import and its injection become available; and
- global initialization sequencing, subject to future lifecycle rules.

It never breaks a name, callable, or operator ambiguity tie.

Declarations are module-internal by default, so moving one between files does
not inherently change visibility. Moving it earlier or later may make a
matching [`forward`](declarations-and-bindings.md#forward-anchors) necessary or
redundant.

Wildcard source selection must eventually define a deterministic,
host-independent order because global initialization may observe it. Exact
source-list, wildcard, generated-source, and durable-build mechanics remain
future build/dependency work.

## Lookup from namespace source

An unqualified name searches the current namespace and then each containing
namespace toward the module root:

```zax
namespace A {
  a : Integer

  namespace B {
    b : Integer
    D :: forward namespace

    namespace C {
      c : Integer

      calculate final : (result : Integer)() = {
        return a + b + c + D.d
      }
    }

    namespace D {
      d : Integer

      namespace E {
        e : Integer
      }
    }
  }
}
```

Inside `C`:

- `c` resolves in `C`;
- `b` resolves in `B`;
- `a` resolves in `A`;
- `D` resolves to the forwarded namespace in `B`; and
- `D.d` remains pending until `D` completes.

Bare `e` does not discover `D.E.e`. Namespace members are not flattened into
their parent.

Eligibility is applied while choosing a lexical scope. A scope containing only
ineligible declarations does not block an eligible outer declaration. Once a
scope has an eligible set, lookup stops there and applies its ordinary
unique/family/ambiguity rules.

Explicit qualification fixes its root. A missing or inaccessible suffix never
falls outward:

```zax
Module.A.B.D.E.e
```

The complete general algorithm, including aliases, shadow permission, pending
suffixes, and diagnostics, is defined by
[declarations and bindings](declarations-and-bindings.md#identifier-namespace-and-paths).

## Named import, direct exposure, and collisions

The ordinary named form is:

```zax
Geometry :: import Module.MyGeometryModule
```

The direct form exposes eligible exported root declarations into the surrounding
namespace:

```zax
:: import Module.MyFoundationModule
```

Direct exposure deliberately accepts a larger source-compatibility surface.
Adding an export to the imported module may introduce another visible name or
callable candidate.

### Collision provenance matters

Two direct declarations in one module namespace are an immediate error except
for a category that explicitly combines them, such as:

- a legal callable overload family;
- namespace reopening; or
- a forward and its matching completion.

Imported or exposed declarations retain different provenance. Several
declarations may occupy one exposed spelling until source demands it:

```zax
:: import Module.FirstGeometryModule
:: import Module.SecondGeometryModule

point : Point
// error when both imports export distinct Point declarations
```

The rules are:

- exact repeated exposure of one declaration coalesces by identity;
- distinct imported declarations are ambiguous when used;
- legal callable members may form one approved overload family;
- namespaces from different module instances never merge by spelling;
- a local declaration does not win because it appears earlier; and
- qualification or selective exposure is the stable repair.

The compiler retains declaration provenance; source order does not infer it.

## Generative imports and injection

An import creates a new module instance even when another import uses identical
source and options. The instance includes:

- one resolved source identity;
- its complete injected declaration environment;
- declarations produced while that source is processed; and
- individualized module-global state.

Compilation may reuse work when semantics remain unchanged. Cache reuse does
not merge module or declaration identity and does not share state.

### Inject declarations before module source

An import body inserts declarations into the target root before the target's own
source resolves:

```zax
ThirdParty :: import Module.ThirdPartyModule {
  UseComplexMath :: alias namespace Module.MyComplexMathImport

  Options :: type {
    debug final : readonly = true
  }
}
```

Injected declarations behave as if the target module author wrote them:

- they use target-module ownership and visibility;
- their newly declared identities belong to that generative module instance;
- they participate in target direct-declaration collision rules; and
- a target declaration with the same direct name is a duplicate error.

Their special capability is lexical access to declarations at the injection
site. In the example, `Module.MyComplexMathImport` resolves in the importer.
`UseComplexMath` preserves that exact namespace and module-instance identity.

This supports deliberate dependency sharing:

```zax
MyType :: type {
  foo : Integer
  bar : String
}

ThirdPartyA :: import Module.ThirdPartyModule {
  UseMyType :: alias type Module.MyType
}

ThirdPartyB :: import Module.ThirdPartyModule {
  UseMyType :: alias type Module.MyType
}
```

If both aliases are exported, these denote one canonical type identity:

```zax
mine : MyType
fromA : ThirdPartyA.UseMyType = mine
fromB : ThirdPartyB.UseMyType = fromA
```

Declarations that `ThirdPartyA` and `ThirdPartyB` define themselves remain
distinct.

An injected import or exposure may contribute several imported candidates with
one name. Those declarations retain imported provenance and use the demand-time
ambiguity rule. Direct declarations written in the injection body do not.

A module can detect whether its importer injected something, and supply a
fallback only when it did not:

```zax
if compile !wellformed{ Module.UseComplexMath } {
  UseComplexMath outward :: import Module.DefaultComplexMath
}
```

[`wellformed{...}`](compile-time-code.md#checking-whether-code-is-well-formed-wellformed)
asks the question under the imported module's own root. `if compile` makes the
choice while compiling, and the
[`outward`](declarations-and-bindings.md#outward-declarations) import becomes a
declaration of the module only when the fallback is selected. The answer is part
of that module instance's semantic inputs: a different injection produces a
different instance.

### Including partials in an imported type

An injection body is also how an importer adds stored members to a type in the
imported module, when that type writes `seal open storage`. It can include a
[partial](partials.md) written elsewhere in the importer:

```zax
Library :: forward module

namespace MyNamespace {
  MyTagging :: partial Library.MyCatalog {
    tag : String = "none"
  }
}

Library :: import Module.LibraryDefinition {
  Module.MyNamespace.MyTagging :: own partial
}
```

`own partial` includes the partial's storage and hook fulfillments in that
module instance's `MyCatalog` without making the partial's names visible to the
library's source, so the library's own code keeps selecting exactly what it did
before. `expose partial` in the same position would also make the partial
visible to all of the library's source. It requires an
`intent<injected-partial-exposure>` acknowledgement because it can change
selections in code the importer cannot see. A partial defined directly inside
the injection body behaves like `own partial`.

Partials declared in a type's own module are included automatically. Complete
inclusion, ordering, and lifecycle rules belong to
[Zax partials](partials.md#adding-storage).

### Import cycles

Recursive import expansion must terminate. A repeated module dependency on one
active import ancestry is an error, including a repeat reached through
injection:

```text
A -> B -> A
```

when both `A` entries count as the same dependency. A diamond remains legal:

```text
A -> B
A -> C -> B
```

Generative instance identity cannot itself define repetition because every
import creates another instance. Exact dependency work must decide whether the
cycle key uses resolved source/content identity, repository identity, selected
version, injection environment, or another stable combination. In particular,
two versions of one repository are not yet classified as necessarily the same
or necessarily different for cycle detection.

This owner establishes only that source cannot require unbounded recursive
module instantiation. Exact repeat identity, dependency acquisition, version
solving, locking, and cache mechanics remain future build/dependency work.

## Visibility and export baseline

Declarations are module-internal by default. Every ordered source file in that
module instance may use them after declaration or forward availability.

Nothing crosses an import unless it is explicitly exported. Importing a module
does not automatically re-export that module or its declarations.

A module may explicitly export a named import binding. Downstream source then
reaches that same generative instance through the exported path rather than
creating another import automatically.

### Exporting with directives

Export is written with the `export`
[compiler directive](compiler-directives.md). Attached, it exports one
declaration:

```zax
[<export>]
MyPoint :: type {
  x : I32
  y : I32
}
```

As a [range](compiler-directives.md#directive-ranges), `[<export=always>]`
exports the declarations written after it, within one opening or file, until
`[<export=never>]` or the end of that region:

```zax
// file: api.zax
[<export=always>]

open final : ()() = {             // exported
}

close final : ()() = {            // exported
}

Math :: import Module.Math        // not exported: ranges never export imports

[<export>]
Geometry :: import Module.Geometry  // exported: an explicit attachment
```

A range exports only declarations written directly inside it. It never exports:

- import bindings or aliases, which always need an attached `[<export>]`, so
  importing never re-exports by itself;
- declarations injected by an importer, or added by partials declared
  elsewhere; or
- the contents of nested namespaces, which need their own range or attachment.

A range ends at its end marker, at the closing brace of its opening, or at the
end of its file. Reordering or moving files therefore never exports something
by accident.

Exact `private`, selective export, alias export, and re-export option sets
remain future visibility work. That work inherits these constraints:

- module-internal is the default;
- export is explicit;
- import is not transitive export;
- visibility is eligibility, not match quality;
- inaccessible candidates do not compete as worse alternatives; and
- order never breaks an export collision.

### Directive state stops at an import

No directive state crosses an `import`. An imported module begins with its own
defaults and its own source, whatever deprecation, export, or panic-check state
the importer is in. Source files brought in by inclusion are different: they
begin with the state in effect where they are included. See
[directive ranges](compiler-directives.md#what-bounds-a-range).

## Namespace and module aliases

An exact namespace alias adds another path to one namespace identity:

```zax
LocalMath :: alias namespace Module.MyComplexMathImport
```

It does not copy declarations, create module state, grant reopening authority,
or structurally merge same-named namespaces.

An import binding is itself the local name of a generative module root. An
exported import binding preserves that module-instance identity.

Type, variable, reshape, and literal-operator aliases are declared by their
domain forms and taught by
[declarations and bindings](declarations-and-bindings.md#non-value-definitions).

## Forward module and namespace roots

A namespace root can be promised before its declaration:

```zax
Tools :: forward namespace
```

A qualified forward may be written from another namespace while declaring its
name in an existing owned namespace:

```zax
namespace A {
  namespace B {
  }
}

namespace C {
  A.B.PendingType :: forward type

  usePending final : ()(
    value : A.B.PendingType
  ) = {
  }
}

namespace A.B {
  PendingType :: type {
  }
}
```

`A` uses ordinary root lookup; `Module.A.B.PendingType` is the explicit-root
form. Every containing component through `A.B` must already be a namespace or
matching namespace forward. The declaration belongs only to `A.B`; physical
placement in `C` creates no short `C.PendingType`.

A module import root has its own category:

```zax
ThirdParty :: forward module
ThirdParty :: import Module.ThirdPartyModule
```

`forward module` does not instantiate the dependency. It establishes a
source-ordered module-root anchor so qualified paths can remain pending. The
later import, including its injection body, creates the generative instance and
completes those paths.

A matching forward is legal even when no use occurs before completion. Tooling
may lint it as redundant. General forward categories, completion, aliases, and
errors are defined by
[declarations and bindings](declarations-and-bindings.md#forward-anchors).

## Costs

Programmers must be able to discover:

- source acquisition and verification;
- processing and static execution performed for an import;
- each generative module instance;
- declarations and global state duplicated by separate imports;
- exact dependencies and state deliberately shared through injection;
- direct-exposure collision analysis;
- pending work caused by forwards and incomplete namespace paths;
- global initialization and destruction governed by future lifecycle rules; and
- rebuild/cache invalidation caused by export, injection, source-list, or
  dependency changes.

No representation, cache, lowering, or linker strategy is promised here.

## Diagnostics

Diagnostics should distinguish:

- unknown root from a known forwarded root;
- a namespace path whose intermediate component is not a namespace;
- namespace declaration in a function, block, or type body;
- unauthorized reopening of an imported or protected namespace;
- missing self-name `shadowable` on the opening that introduces a shadow;
- same-scope direct duplicate from demand-time imported ambiguity;
- inaccessible declaration from absent declaration;
- missing suffix below a complete root from a pending suffix;
- mismatched or uncompleted namespace/module forward;
- distinct generative declarations with equal spelling or shape;
- target-source collision with an injected direct declaration;
- recursive or unbounded module import, including an injection-created edge;
- non-exported declaration at an import boundary; and
- missing or conflicting explicit re-export.

Exact identifiers and presentation remain later diagnostics design.

## Source stability

Compatibility events include:

- moving a declaration before or after a use that depends on a forward;
- changing source-file or wildcard expansion order;
- moving a declaration between module or namespace owners;
- adding, removing, renaming, or changing visibility of an export;
- replacing a named import with direct exposure;
- changing an import's source identity or injection body;
- replacing separate module instances with one exact injected dependency, or
  the reverse;
- changing a namespace alias target;
- adding a declaration that collides with injected source;
- making a private declaration eligible; and
- adding another imported candidate under an exposed name.

Source, file, declaration, import, module, generation, and cache order never
silently resolve an ambiguity.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, build
contract, package-manager specification, ABI promise, or compiler mapping.

Future work owns:

- exact `private`, selective export, alias export, and re-export options;
- package fetching, Git resolution, versions, locking, canonical content
  identity, and cache control;
- module/global initialization, destruction, `once`, and concurrency;
- open namespace authority;
- source and declaration reflection; and
- dynamic loading and runtime name lookup.

Those mechanisms must preserve protected local roots, source-ordered name
availability, generative module identities, exact injected sharing, explicit
export, eligibility-before-preference, and order-independent ambiguity.
