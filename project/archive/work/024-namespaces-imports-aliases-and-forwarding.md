# 024: Namespaces, imports, aliases, and forwarding

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `024` |
| Created | 2026-09-16 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete package acquisition, dependency solving, build caching, generic declarations, partial-type extension, compile-time execution, reflection, global/module initialization, dynamic loading, or implementation architecture |

This completed record is retained only for targeted historical audit. Current
language meaning belongs to the promoted owners identified below.

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `024` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for how Zax declarations become
nameable, available, and stable across lexical scopes, types, namespaces,
modules, imports, aliases, visibility boundaries, and incomplete/forward
declarations.

The
[namespace, aliasing, and forwarding maintainer notes](../raw/namespace-aliasing-forwarding-maintainer.md)
are primary maintainer-supplied input. The language maintainer will populate
that file before work item `024` begins. Read and disposition its complete
contents as the latest design evidence. It may add to, replace, supersede, or
obsolete any portion of the legacy corpus; retain legacy pages as evidence for
useful material the refreshed notes do not cover.

The indexed
[namespaces, imports, and visibility input](../raw/namespaces-imports-and-visibility.md)
preserves exact literal-operator alias identity, import-order independence,
collision, visibility, and forwarding pressure discovered by literal work.

### Motivating pressure

Current declaration design already establishes:

- one ordinary identifier namespace per lexical scope;
- deterministic nearest-scope root lookup;
- qualified suffix resolution only inside the selected root;
- pending suffixes below incomplete declarations;
- explicit shadow permission;
- transparent type aliases as another name for one identity; and
- no source, declaration, import, or module order tie-break for current operator
  selection.

Several later concepts require a complete availability model:

```zax
MyNamespace.x'first' x'second'
// Both literal prefixes must independently identify one exact declaration.

MyType :: forward type
MyAlias :: alias type ExistingType
```

Literal merge needs exact aliases to preserve declaration identity. Optional
platform declarations such as `Legacy.WChar` need future static source to ask
whether a declaration is available without first failing ordinary name
resolution. Generics, partial types, compile-time generation, function aliases,
and eventual reflection all depend on stable answers to which declaration a
name denotes and who may expose or complete it.

This naming/module boundary should be established before compile-time source
selection, generics, partial extensions, and reflection build larger mechanisms
on top of it.

### Known assumptions

- The first component of a qualified path resolves once through ordinary lexical
  lookup. Failure of a later component never backtracks to another root.
- Source, declaration, import, module, or generated order does not silently
  resolve ambiguity.
- A transparent type alias preserves its target identity; an explicit identity
  declaration creates another identity.
- A reference binding or same-place declaration alias is not automatically a
  namespace/type/callable alias.
- An exact literal-operator alias preserves declaration identity strongly enough
  for qualified and unqualified segments to merge.
- Visibility determines eligibility before preference; inaccessible
  declarations do not compete as worse public candidates.
- A forward declaration categorizes an incomplete declaration and must
  eventually match one completion. It is not an implementation body, implicit
  import, or source-order preference.
- Platform-optional declarations create compile-time availability pressure but
  do not authorize reflection or conditional-compilation syntax here.
- Namespace and module lifetime/global initialization consequences remain
  visible without being completed by this item.

### Known inclusions

- Lexical, type-member, namespace, module, and imported name boundaries.
- Namespace identity versus module identity and file/package organization only
  where programmers need the distinction to predict lookup and visibility.
- Qualified paths, stable roots, pending suffixes, shadowing, and collisions.
- Import into a named namespace versus deliberate exposure into a surrounding
  namespace.
- Export/re-export, private/public eligibility, selective exposure, and
  diagnostics.
- Exact aliases for types, namespaces/modules, functions/callables, operators,
  and literal operators at the depth required to define declaration identity
  and lookup.
- The boundary among transparent aliases, compatible visible prototypes,
  references/same-place aliases, imported names, and new identities.
- Forward declarations for types, namespaces/modules, values, callables,
  operators, and literal operators when each category has a demonstrated need.
- Completion matching, dependency cycles, pending resolution, and errors when a
  declaration never completes.
- Literal merge through qualified/unqualified aliases.
- Operator/callable discovery and ambiguity consequences.
- Platform-optional declarations and the exact handoff to future static
  source-resolution/capability work.
- Source stability, formatting, diagnostics, tooling, and eventual reflection
  consequences.
- Complete disposition of the maintainer notes, focused raw input, and reached
  legacy alias/forward/namespace material.

### Known starting boundaries

- Package fetching, repository URLs, dependency-version solving, lockfiles,
  caches, generated files, and build-system execution.
- Complete module initialization, destruction order, startup concurrency, and
  global/`once` lifetime scheduling.
- Complete compile-time execution, speculative source-enclosure syntax, and
  static capability queries.
- Generic declaration, deduction, specialization, constraint, and alias-family
  syntax.
- Partial/open type authority, external member/operation extension, and
  cross-module layout evolution.
- Complete callable compatibility, function values, lambda capture, chaining,
  and dynamic dispatch.
- Reflection metadata shape, declaration enumeration, and runtime type
  information.
- Dynamic module loading or runtime name lookup.
- Compiler symbol tables, dependency-graph algorithms, mangling, object formats,
  and linker implementation.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether a namespace and module are distinct declaration kinds or one
  declaration with different source/build roles.
- Exact import/export/re-export and selective-exposure syntax.
- Which alias categories share one declaration form and which need structured
  type/callable/operator forms.
- Whether an exact callable/operator alias reuses one minted implementation or
  creates a new visible declaration contract.
- Which declaration categories benefit from explicit `forward`, and which can
  rely on dependency-directed pending resolution.
- Whether namespace/module cycles are always errors or only become errors when
  initialization, layout, or completion dependencies cannot be ordered.
- How an optional declaration is named inside a future non-failing static source
  query.
- Which module/name changes are source, binary, build-cache, or import-contract
  compatibility events.

### Initial stopping guidance

Stop when the work has:

- established a teachable mental model for scopes, namespaces, modules, imports,
  aliases, visibility, and forwarding;
- defined deterministic name and qualified-path resolution without
  order-dependent fallback;
- separated alias identity from reference binding, visible-prototype adaptation,
  and new identity declaration;
- defined import/export/re-export and collision behavior at programmer depth;
- defined useful forward categories, completion matching, pending resolution,
  and cycle diagnostics;
- reconciled literal/operator/callable alias identity and discovery;
- dispositioned optional declaration availability without prematurely designing
  compile-time execution or reflection;
- assigned module lifetime, build/package, generic, partial-type, callable, and
  implementation consequences to current or indexed future owners;
- dispositioned all useful refreshed and reached legacy material; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `025`, or
design complete build/dependency management, generics, partial types,
compile-time execution, reflection, function values, or module initialization
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Namespace, aliasing, and forwarding maintainer notes](../raw/namespace-aliasing-forwarding-maintainer.md) -
  will contain the maintainer's refreshed primary input before this work item is
  assigned; read and disposition it completely.
- [Raw namespaces, imports, and visibility](../raw/namespaces-imports-and-visibility.md) -
  preserves exact literal-alias, collision, qualification, visibility, and
  forwarding pressure already discovered.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns current lexical namespace, qualified paths, pending suffixes, shadowing,
  visibility eligibility, forwarding, callable aliases, and declaration forms.
- [Identity types](../../language/identity-types.md) - owns transparent aliases
  versus distinct identity declarations.
- [Literal source and operators](../../language/literal-source-and-operators.md) -
  supplies exact alias identity pressure from qualified/unqualified literal
  merge.
- [Legacy aliases](../../alias.md), [forward declarations](../../forward.md), and
  [namespaces/imports](../../namespacing.md) - preserve the principal legacy
  syntax, use cases, and unresolved module/name input to disposition by value.

### Consequence-driven

- [Function invocation](../../language/function-invocation.md),
  [operators](../../language/operators.md), and the
  [operator catalog](../../language/operator-catalog.md) when callable/operator
  alias identity, visible contracts, lookup, or ambiguity becomes material.
- [Raw callable selection](../raw/callable-selection.md) when an exact alias,
  compatible visible prototype, minted implementation, or preference boundary
  cannot be separated locally.
- [Raw build and dependencies](../raw/build-and-dependencies.md) when module
  identity crosses into package acquisition, versioning, caching, source lists,
  generated files, or build execution.
- [Raw partial types](../raw/partial-types.md) when module ownership, external
  completion, or extension authority becomes concrete.
- [Raw compile-time execution](../raw/compile-time-execution.md) when optional
  declaration availability or a non-failing source-resolution enclosure becomes
  concrete.
- [Raw global and once lifetimes](../raw/global-and-once-lifetimes.md) when a
  namespace/module boundary constrains global construction, dependency ordering,
  destruction, or concurrency.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md) when
  generic aliases, specialization identity, or type-dependent availability
  becomes material.
- Relevant legacy module, compiler-directive, meta-type, meta-function, partial,
  or function material only when a concrete question cannot be resolved from
  the primary notes, required legacy pages, and current owners.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with programmer source that introduces a namespace, imports or exposes one
name, creates an exact alias, resolves a qualified path, and forwards one
declaration. Explain which declaration each name denotes before designing module
loading, dependency graphs, or compile-time availability.

Treat the maintainer notes as the latest primary evidence once populated. Legacy
pages remain available for useful unrefreshed cases but do not override the
maintainer notes merely because they contain more text.

## Working record

### Initial reconstruction for review

Everything below is raw evidence or candidate analysis. It records a possible
coherent model and the questions that would have to be answered before any
finding could be aligned or promoted.

Subsections explicitly marked as aligned record agreement for this work item.
They remain non-authoritative until separately promoted.

### Review entry point

The current candidate begins with three separate operations:

1. A **namespace** organizes declarations inside one module-local root.
2. A **module import** makes another module root available under a chosen local
   name.
3. An **exposure** adds selected names from an already available namespace or
   module root to the surrounding ordinary identifier namespace.

These operations must not be collapsed into "copy all declarations here." A
named import supplies a stable qualification root and preserves the imported
declarations' identities. Direct exposure is convenient, but it deliberately
accepts collision and source-stability risk.

```zax
Geometry :: import MyGeometryModule

point : Geometry.Point
distance := Geometry.distance(point, origin)
```

The module's source may organize `Point` and `distance` into namespaces, but the
local name `Geometry` is the importer's path to that module root. Importing the
same source again as `DrawingGeometry` creates another generative module
instance. Its declarations, including apparently identical types and functions,
have different identities unless both instances explicitly preserve an
injected declaration identity.

Maintainer review has aligned that `alias type` may abbreviate the complete
property shape accepted where a type is explicitly declared:

```zax
MyDeepView :: alias type MyType readonly & deep
```

Explicit properties on a new alias or use override the inherited property on
that axis. Unspecified properties come from the aliased type or alias and then
from the common language defaults. The alias retains the canonical target
identity and does not make an unavailable capability available on an actual
source.

Postfix `_` remains the one-word keyword escape:

```zax
erase_ cursor
```

The semantic word remains `erase`; `_` only suppresses a keyword role.
Maintainer review has also aligned that `bare{...}` exists as a complete-source
keyword neutralizer:

```zax
result := bare{ if + while } + fallback
```

Neutralization is intentionally strict: every non-operator keyword role inside
the payload is suppressed, including a role that would have been convenient.
Nesting is intentionally unavailable because the outer enclosure neutralizes
the inner `bare` opener.

Namespace syntax, lookup, placement, visibility defaults, variable aliases,
forward categories, and receiver ownership have now received maintainer review.
The remaining export-directive, lifecycle, build, generic, capture, partial, and
reflection mechanics have explicit deferred destinations. The aligned model is
ready for a documentation-fit dry run.

### Candidate programmer model

#### Module roots, namespaces, and files

Each module has its own unnamed root scope. Source files selected for that module
contribute to the same root; a file is organization and an ordered source unit,
not an implicit namespace and not a globally unique qualification root.

A namespace is a named declaration scope beneath that module root or another
namespace. Namespace names use the type-like capitalization intent and ASCII
identifier domain. The refreshed notes additionally say namespace names are
alphanumeric only; exact identifier-token consequences still need checking
against the eventual complete identifier grammar.

Namespace declarations use:

```zax
namespace Car {
  namespace Comfort {
    namespace Stylish {
    }
  }
}

namespace Car.Comfort.Stylish {
  // Contributes to the same namespace declaration.
}
```

`namespace Foo.Bar` resolves `Foo` as an ordinary root; every intermediate
component must already be a namespace or forwarded namespace. Only the final
component may be introduced or reopened. `namespace Module.Foo.Bar` explicitly
selects the current module root.

Namespace declarations are allowed only at module root or inside another
namespace. They are not allowed inside functions, ordinary blocks, or type
bodies. This keeps a namespace a compile-time module declaration scope rather
than a named local lifetime, per-call value container, or ambiguous type-owned
storage surface.

Reopening a namespace within its owning module is a category-specific
continuation of one namespace declaration, not ordinary same-scope
redeclaration. Equal spelling does not merge namespaces from different module
instances. If two directly exposed modules both contain `Utilities`, those are
two declarations competing for one spelling unless they are exact exposures of
the same declaration.

Reopening follows module-instance provenance:

- ordinary source may reopen a namespace owned by its own module instance;
- an import injection acts as target-module source and may reopen a namespace
  owned by that target instance;
- importing or creating an exact namespace alias does not transfer reopening
  authority; and
- language-protected namespaces such as `Scalars` cannot be reopened by user
  source.

No explicit authority token is needed. Future partial/extension work may define
controlled external contributions without making ordinary imports extension
authority.

#### Namespace-opening self-name shadow permission

`shadowable` on a namespace opening is local to that opening. It permits a
declaration in that body to hide the namespace's own name:

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

Only the opening that first introduces the shadowing declaration or forward
needs `shadowable`. The later definition completes the existing forwarded
member, so it neither introduces another shadow relationship nor repeats the
permission.

`shadowable` is not an identity-wide namespace property and does not grant
permission to hide arbitrary outer declarations. Without it:

```zax
namespace Utilities {
  Utilities :: type { // error: this opening does not permit self-name shadowing
  }
}
```

After the nested member exists, later reopenings see it through ordinary
source-ordered lookup. `Module.Utilities` remains the explicit path to the
namespace itself. A protected namespace cannot use this user-source permission.

The maintainer notes require every module, including an application module, to
have a `module.zax` source that establishes its source-file set and source-file
order. That order is materially observable for global initialization, but it
also establishes when an unqualified root has been declared or forwarded. It
must not become an ambiguity or overload preference:

```text
source-file order -> root-name availability and initialization sequencing
forward declaration -> earlier category anchor for a later completion
completed declarations -> final suffix, type, and callable validation
```

The exact `module.zax` source-list syntax, wildcard expansion order, generated
source handling, and interaction with global dependency analysis remain open.
Wildcard order cannot be left to filesystem enumeration, locale, host case
folding, or checkout accident if it affects initialization. Lifecycle-sensitive
code may ultimately require an explicit list even if deterministic wildcard
expansion remains convenient.

#### A module-local root, not a universal absolute namespace

The current declaration owner uses `Module` as a stable qualification root, and
the refreshed notes say each module has its own understanding of root and that
there is no absolute namespace path. Maintainer review aligns `Module` as the
protected binding meaning "this module's root":

```zax
value : Module.Geometry.Point
```

Code inside an imported module would resolve its own `Module`, not the
importer's root and not a process-wide namespace. The importer's `Geometry`
binding is a local path to the imported root; it does not rename that module's
internal `Module`.

`Module` cannot be redeclared, shadowed, replaced by an import, or retargeted by
an alias. An importer cannot change the meaning of an imported source file's
ordinary `Module`-rooted names merely by choosing another local import name.
Injected declarations enter that imported module's protected root under the
separate rules below.

#### Language-provided root injection

Every module instance begins with language-provided declarations and operations
that are part of Zax itself rather than an independent library. Independent
libraries still require explicit import. The conceptual order is:

```text
language-provided root declarations
-> importer-supplied injection
-> the module instance's own source declarations
```

The `Scalars` namespace is automatically present in every module root as a
protected exact alias to one language-owned declaration:

```zax
Scalars.Integers.I32
Module.Scalars.Integers.I32
```

It is not freshly defined by each generative module instance. Nested namespaces
reach it through ordinary lexical lookup rather than receiving repeated local
injections. Qualification through two imports therefore preserves one
language-provided identity:

```zax
first : ThirdPartyA.Scalars.Integers.I32
second : ThirdPartyB.Scalars.Integers.I32 = first
// Same language-owned I32 identity.
```

This exception does not weaken generative module identity. Declarations written
by `ThirdPartyA` and `ThirdPartyB` remain distinct; their `Scalars` bindings are
exact aliases to declarations owned outside both instances.

Language-provided short names such as `Integer`, `I32`, and `Boolean` are also
automatically available rather than requiring a library import. Whether all
such short names are protected against shadowing, or only protected roots such
as `Module` and `Scalars` are, remains to be aligned.

#### Namespace identity and module identity

The aligned distinction is:

- a **module instance identity** identifies one generative import of resolved
  module source under one complete injected declaration environment and owns an
  isolated root;
- a **namespace identity** is its owning module-instance identity plus its
  declaration path within that root;
- an **import binding** is a local name through which another module root is
  reached; and
- a **file** contributes source to a module but contributes no identity layer
  by itself.

Two imports of the same source are distinct:

```zax
Geometry :: import MyPinnedGeometryModule
DrawingGeometry :: import MyPinnedGeometryModule

first : Geometry.Point
second : DrawingGeometry.Point = first
// error: the two imports generated distinct Point identities
```

The same rule naturally allows two versions under distinct local roots:

```zax
GeometryV1 :: import MyGeometryV1Module
GeometryV2 :: import MyGeometryV2Module

oldPoint : GeometryV1.Point
newPoint : GeometryV2.Point
// Distinct declaration identities even when source names and layouts match.
```

Each import also has individualized module state. Compilation may reuse work
when doing so preserves these identities and state, but cache or generated-code
reuse does not merge module instances. `compatible` posture may permit an
explicit structural relationship; it never makes declarations from two module
instances identical. Exact sharing occurs only when both instances use an
injected alias to the same declaration or imported namespace identity.

#### Deterministic path resolution

The current declaration rules plus the aligned forward model supply the
candidate lookup algorithm:

1. Classify the source request: ordinary identifier, literal prefix, structured
   operator form, flow label, or another category.
2. Require an ordinary path's first component to have been declared, exposed,
   or forwarded by that source position, then resolve it once by nearest
   lexical scope.
3. Apply visibility eligibility before any callable or operator preference.
4. Collapse multiple paths only when they denote the exact same declaration.
5. Apply category-specific grouping, such as a legal overload family, rather
   than treating source order as preference.
6. Resolve each suffix only inside the previously selected declaration.
7. If that declaration is incomplete, keep only its suffix pending.

An unrecognized root is diagnosed without searching the rest of the ordered
source base for a later declaration. `forward` is the programmer's explicit
promise that the root is real and will receive a compatible completion.
Syntactic parsing of `myGlobal = value` or `myGlobal.hello()` does not require
the symbol; the forward changes name resolution and permits semantic checks to
remain pending.

No failed suffix retries another root:

```zax
namespace Car {
  namespace Comfort {
    namespace Stylish {
      namespace CoolLook {
        Stylish.MyType :: forward type
        // Stylish means Car.Comfort.Stylish, not Car.Stylish.
      }
    }
  }

  namespace Stylish {
  }
}
```

The syntax is legacy/candidate source, but the nearest-root consequence is
already current. A forward and its completion are one root-binding relationship,
so completion cannot make an existing path retry lexical lookup. An exact alias
completion may establish the anchor's canonical target without changing the
selected root spelling.

Unqualified roots search from the current namespace outward:

```zax
namespace A {
  a : Integer

  namespace B {
    b : Integer
    D :: forward namespace

    namespace C {
      c : Integer

      func final : (result : Integer)() = {
        return a + b + c + D.d
      }

      // Bare e does not discover D.E.e.
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

Inside `C`, `c` resolves locally, `b` in `B`, and `a` in `A`. `D` resolves to
the forward in `B`; `D.d` stays pending until that namespace completes.
`D.E.e` is a legal explicit path, but namespace contents are not flattened into
the enclosing unqualified search.

Eligibility is filtered while choosing the lexical scope. If a scope contains
only declarations that are ineligible to this source, lookup continues outward.
Once a scope has one or more eligible declarations, lookup stops there and
ordinary unique/family/ambiguity rules apply. Explicit qualification fixes its
root and never falls outward after an inaccessible or missing suffix.

#### Shadowing and imported roots

Import bindings, namespace names, aliases, values, and types participate in the
one ordinary identifier namespace. Nested source may hide an outer binding only
when that outer declaration grants `shadowable`; the permission is not inferred
from import provenance.

```zax
Geometry shadowable :: import MyGeometryModule

if condition {
  Geometry :: type {
  }
}
```

Exact modifier placement is illustrative. If both roots must remain available,
the programmer establishes another name before shadowing. There is no parent
search operator.

Directly exposed names create a harder question because they may not have a
local declaration on which to write `shadowable`. Import/exposure design must
decide whether the exposure declaration carries shadow permission for its
introduced names, whether those names are never shadowable by default, or
whether selective aliases are the required stable repair.

### Imports, exposure, export, and visibility

#### Named import versus direct exposure

The refreshed and legacy notes preserve two forms:

```zax
Geometry :: import MyGeometryModule
:: import MyGeometryModule
```

Under the candidate model, the first introduces one local module-root binding.
It does not copy declarations into the surrounding namespace. The second both
imports the module dependency and exposes its eligible exported root
declarations directly into the surrounding namespace.

Named import should be the stable ordinary form because it:

- bounds collisions under one explicit root;
- lets multiple versions coexist;
- makes dependency provenance visible;
- avoids changing unrelated unqualified source when an imported module adds an
  export; and
- gives diagnostics and tools a durable qualification path.

Direct exposure remains useful, especially for a deliberately selected
foundation surface, but is a source-compatibility commitment.

Subnamespaces preserve their relationship to the imported root:

```zax
Geometry :: import MyGeometryModule

value : Geometry.Shapes.Point
```

`Shapes` does not become an unrelated importer-owned namespace merely because
the module is locally called `Geometry`.

#### Exposure collisions

The old "first declaration/import wins" rule is incompatible with the current
order-independent model and is rejected by the refreshed goals. This source
cannot silently choose one `Point`:

```zax
:: import FirstGeometryModule
:: import SecondGeometryModule

point : Point
```

Maintainer review aligns collision behavior by declaration provenance:

- exact repeated exposure of the same declaration can coalesce by identity;
- distinct imported or exposed declarations may retain one spelling and become
  ambiguous when that spelling is used;
- legal callable overload members can form one category-approved overload
  family, with no provenance preference;
- distinct namespaces from different modules do not merge by spelling;
- two direct declarations in one module scope are a declaration-time duplicate
  error except for a category-approved overload family, namespace reopening, or
  forward completion;
- a declaration injected directly into a module is treated as if the module
  author wrote it and therefore participates in the direct-declaration rule;
- a local declaration does not silently win over an imported candidate merely
  because it appears earlier; and
- qualification, selective exposure, or an explicit alias repairs the conflict.

The compiler retains provenance, so it can distinguish two direct declarations
from several imported candidate bindings without guessing from text or order.

#### Import injection

An import body inserts declarations into the new module instance before that
module's own source resolves:

```zax
ThirdParty :: import Module.ThirdPartyModule {
  UseComplexMath :: alias namespace Module.MyComplexMathImport

  Options :: type {
    debug final : readonly = true
  }
}
```

The injected declarations appear as if the imported module author wrote them:

- they enter the imported module instance's protected `Module` root;
- they use that target root's ordinary direct-declaration collision rules;
- their ownership, visibility, and generated identities belong to that module
  instance; and
- a target-source declaration with the same direct name is a duplicate error,
  not an import-order override or demand-time imported-name ambiguity.

Their one special capability is lexical access to declarations available at the
injection site. `UseComplexMath` therefore preserves the exact namespace and
module-instance identity reached through the importer's
`Module.MyComplexMathImport`. A declaration defined afresh in two injection
bodies remains generative and distinct even when its source text is identical.

The same rule applies to injected type aliases:

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

When both aliases are exported, `MyType`, `ThirdPartyA.UseMyType`, and
`ThirdPartyB.UseMyType` retain one canonical type identity. By contrast,
`ThirdPartyA.ExportedType` and `ThirdPartyB.ExportedType` are distinct when each
is declared by its own generative module instance. Alias properties may produce
different qualified profiles without changing the shared canonical identity.

Injected import/exposure operations may themselves contribute several imported
candidates with one spelling. Those retain imported provenance and follow the
demand-time ambiguity rule above; direct declarations in the injection body do
not.

An imported module may use a future compile-time availability query to declare
or import a fallback only when no injected symbol is present. The selected
injection and resulting conditional source are part of that module instance's
semantic inputs. Injection-created dependency edges participate in the same
recursive-import check; injection cannot conceal unbounded module expansion.
Exact repeat identity across versions and injection environments remains
dependency work.

No separate automatically shared "super module global" is introduced. A parent
can explicitly share one imported module instance, including its state, by
injecting exact namespace aliases into other module instances. A broader shared
global facility remains deferred until a concrete use cannot be expressed by
that mechanism.

#### Export is eligibility, not copying

The aligned visibility baseline is:

- lexical/private eligibility inside the declaring owner;
- module-internal availability across that module's source files;
- export eligibility through an import; and
- explicit re-export or selective exposure by an importing module.

Declarations are module-internal by default. Every ordered source file in that
module instance may name them after ordinary declaration/forward availability;
a source file is not a namespace or default visibility boundary. Moving a
declaration between files does not inherently change visibility, although its
new source position may require or make redundant a matching forward.

Nothing crosses an import unless it is explicitly exported. Importing a module
does not automatically re-export it or any of its declarations. A module may
explicitly export a named import binding so another importer can reach that
generative module instance through the exported path.

Exact `private`, `[[export]]`, selective export, and re-export syntax remains
deferred to compiler-directive/visibility review. A matching forward is legal
even when no intervening source required it, provided its category agrees with
the completion; tooling may lint that redundancy.

An imported module root contains only declarations eligible through that
module's export contract. Ineligible declarations are removed before overload
preference and cannot tie, defeat, or become an unavailable-best alternative to
a visible declaration.

Exporting an import is explicit re-export, not a transitive default:

```zax
Utilities :: import MyUtilitiesModule
// Candidate: Utilities is available locally but is not re-exported unless
// this module explicitly declares that API surface.
```

Selective exposure needs to support:

- one declaration;
- one namespace subtree only when deliberately requested;
- one exact callable variable family;
- one exact literal-operator prefix where requested;
- renaming;
- re-export; and
- explicit conflict diagnostics.

Exact syntax is open. A wildcard may be acceptable as a conscious compatibility
surface, but it must not gain first-import-wins behavior.

#### Alias visibility

An alias declaration has its own name and visibility, while its denotation can
remain the exact target:

```zax
InternalType :: type {
}

PublicType :: alias type InternalType
```

This could intentionally export the `InternalType` identity under only the
`PublicType` spelling. That is different from making the original spelling
visible. The alias author must be eligible to name the target, and re-exporting
an imported target may require explicit module authority.

The design still must decide:

- whether an exact declaration alias can ever increase callable or operator
  eligibility rather than merely provide another eligible path;
- how a private implementation may support a new public callable contract
  without exposing the private declaration itself;
- whether export permission is attached to the target, the alias edge, or both;
  and
- what diagnostics reveal about an inaccessible canonical spelling.

Current compatible-prototype behavior provides one useful boundary: a new public
callable declaration may explicitly reuse an accessible private helper because
it is a new API contract, not publication of the helper declaration.

### Alias families

The word "alias" currently covers materially different operations. Keeping them
separate is necessary for identity, visibility, lookup, reflection, and cost.

#### Transparent type-use aliases

The uncontested core is:

```zax
MyShortName :: alias type SomeLongTypeName
```

Both names denote one canonical type identity. The alias creates no runtime
value, representation, conversion, constructor, destructor, or overload domain.
Retargeting it is a source-compatibility event.

The refreshed notes broaden the target to everything allowed where a type is
explicitly declared:

```zax
MyDeepType :: alias type MyType readonly & deep
```

Maintainer review aligns an overlay model. A concrete type alias may carry base
identity, reference/pointer shape, qualifiers, transfer stance, and
compatibility posture. Resolution proceeds from less local to more local:

```zax
DeepView :: alias type MyType readonly & deep
CopyView :: alias type DeepView writable copy
```

`CopyView` denotes the canonical `MyType` identity with the inherited reference
shape and the more local `writable copy` properties. Properties written on the
new alias override inherited properties on the same axis. Properties it does
not state come from `DeepView`, then `MyType`, then the common language defaults.

Overlay changes a requested declaration/type profile, not an existing value.
Binding `CopyView` to a source that cannot provide writable access still fails;
an alias does not manufacture mutability, writability, replacement permission,
transfer support, or structural compatibility. Reflection must keep canonical
identity, resolved properties, and the source alias path distinct.

How partially applied generic aliases carry or override this property profile
is deferred to generic work. This item considers only aliases that resolve to
one complete concrete profile.

#### Generic and computed type aliases

A fully selected generic specialization can have another name:

```zax
// Illustrative generic syntax.
MyConcreteType :: alias type MyType$(TypeA, TypeB)
```

The alias would denote that one concrete specialization; it would not create a
new identity. Partial application, an alias that remains generic, qualifier
parameters, generic forwarding, and exact `$` syntax belong to generic work.

A type-returning compile-time call is also explicit pressure:

```zax
// Illustrative; user-defined type-returning functions are not current.
MySelectedType :: alias type giveMeAType()
```

The aligned direction is that the call must finish during applicable static
processing and produce one concrete known type before the alias resolves. The
alias never denotes a random future type. This item does not establish
type-returning function or execution syntax; generics and compile-time work must
define that mechanism while preserving the concrete-at-alias-boundary rule.

#### Callable prototype type aliases

The refreshed form is explicit:

```zax
MyFuncPrototype :: alias type type of myFunc
```

This aliases the callable's type/prototype, not the declaration `myFunc` and not
its declaration identity:

```zax
myAlternative final : MyFuncPrototype = myFunc
```

`myAlternative` is a value/declaration with its own lifetime and callable
binding behavior. It is not an additional lookup spelling for `myFunc`.
Likewise, a compatible visible prototype:

```zax
betterOperation final : BetterPrototype = operation
```

is a new visible callable declaration that reuses one already minted
implementation under the current invocation model. It may relabel slots, change
defaults, or adapt outward result policy when compatibility permits. Those
changes prove it is not an exact declaration alias.

Maintainer review aligns a receiver dimension on function prototypes:

- `bound` means the prototype has one receiver slot of a known type and `_` is
  available to its implementation;
- `unbound` means the prototype has no receiver slot and `_` is unavailable;
- free functions are implicitly `unbound`;
- functions declared in a type are implicitly `bound` to that type;
- either word may be written explicitly where intent needs to be visible;
- an explicitly `unbound` function inside a type remains owned and qualified by
  that type without receiving an instance; and
- a type-owned `once bound` function retains its receiver slot when a
  type-qualified call supplies the `Nothing` receiver state.

Prototype binding is distinct from capturing one receiver instance. `type of`
uses the declared variable's selected static function type and never captures
or executes an instance expression:

```zax
MyType :: type {
  value : Integer
  foo final : ()() = {
  }
}

myType : MyType

FunctionPrototypeBoundToMyType :: alias type type of myType.foo

myBoundFunc : FunctionPrototypeBoundToMyType = {
  _.value = 1
  _.foo()
}

myBoundFunc()        // error: the bound prototype still needs a receiver
myType.myBoundFunc() // error: myBoundFunc is not a MyType member
```

A future receiver-application/capture operation can create a closed callable
that stores or borrows the receiver:

```zax
// Suggested future syntax; not accepted by this item.
callableMyBoundFunc := [&myType] >> myBoundFunc
callableMyBoundFunc()
```

The operation must define value/reference capture, lifetime, repeated
invocation, overload selection, and the resulting receiverless callable
prototype. Its close relationship to lambdas, partial application, and callable
composition should be resolved together by future function-composition work.

#### `final`, `once`, and receiver binding

Every `final` function has one fixed implementation and no replaceable
function-value slot per instance or per type. A type may own and qualify that
implementation, but ownership does not create instance storage:

- `final bound` is one fixed implementation requiring a receiver;
- `final once bound` additionally permits a type-qualified invocation with the
  `Nothing` receiver state;
- `final unbound` is one fixed type-owned implementation requiring no receiver;
- `varying unbound` without `once` has ordinary per-instance replaceable
  function storage;
- `once varying unbound` has one replaceable function slot for the complete
  type; and
- `once final unbound` adds neither sharing nor a call form and is therefore a
  non-acknowledgeable intent error.

The last error should explain the likely repairs: remove `once` for a fixed
receiverless implementation, use `varying` for one shared replaceable slot, or
use `bound` when a `Nothing`-receiver type call was intended.

Parameter and result names, defaults, result initialization state, transfer
stances, and receiver origin also need a precise split between exact prototype
identity and compatible visible-contract adaptation. The notes say names belong
to prototypes while different name usages may remain compatible. An exact type
alias should preserve the complete original prototype; compatibility may then
permit assignment/adaptation without pretending the types were textually
identical.

#### Anonymous type aliases

An alias target may itself create an anonymous type identity:

```zax
MyRecord :: alias type :: type {
  foo : Integer
  bar : String
}
```

The candidate interpretation is that the anonymous type expression creates one
identity and `MyRecord` gives that identity its first accessible name. The alias
does not create a second identity. This is observably different from an alias to
the type of an existing anonymous-typed value only in where the anonymous
identity originated.

#### Reshape aliases

The refreshed notes accept:

```zax
MyReshaper :: alias reshape Reshaper
```

This should be an exact alias of one no-storage mapping declaration. It must not
copy the mapping, reverse its direction, rematch paths against declaration
order, or create another mapping identity. Whether all exact non-type
declaration aliases share one syntax is open.

#### Exact variable aliases

`alias variable` adds another declaration name for one exact variable or
approved polymorphic variable family:

```zax
handler varying : Callback = firstHandler
activeHandler :: alias variable handler

activeHandler = secondHandler
// Replaces the same varying slot denoted by handler.
```

Functions are variables, so no separate `alias callable` category is needed:

```zax
parse final : (result : Value)(input : String) = {
}

parse final : (result : Value)(input : Bytes) = {
}

decode :: alias variable parse
```

`decode` exposes the same callable declarations and selection family under
another name. It creates no function-value slot, capture, initialization, or
compatible wrapper. A typed visible declaration remains the way to select one
unambiguous member and present another contract:

```zax
selectedParse final :
  (result : ExactType)(input : UseThisForm) = parse

Prototype :: alias type type of selectedParse
wrapper final : Prototype = selectedParse
```

The prototype carries callable type, not provenance. Reapplying it directly to
the original family may repeat selection and can remain ambiguous when several
declarations are compatible. A reference binding is also distinct: it creates a
runtime reference value with its own lifetime and declaration permissions,
whereas `alias variable` is a no-storage exact declaration alias.

#### Exact declaration aliases

Literal merge demonstrates a category not supplied by type aliases or callable
prototype aliases: another name/path that denotes the exact same declaration.

```zax
MyLiterals.x'first' <|> x'second'
// Valid only if x independently resolves through an exact alias or exposure
// to the MyLiterals.x literal declaration.
```

Canonicalizing through the alias must produce one literal declaration identity.
Equal result type, equal owner type, equal body text, or compatible prototype is
insufficient. The same identity consequence controls
`intent<same-prefix-literal-join>` through exact aliases.

Imports, selective exposure, re-export, namespace aliases, and literal-prefix
aliases likely need this exact-declaration operation. It should preserve:

- the target declaration identity;
- the exact callable/operator prototype;
- one minted implementation;
- owner and receiver relationships;
- protected/open classification;
- availability requirements; and
- source-independent merge and ambiguity behavior.

The alias edge may still have its own spelling, source location, deprecation,
visibility, and export metadata. Future reflection must distinguish that edge
from the denoted declaration.

Literal operators are aligned as type- or namespace-owned `final` callable
declarations with `unbound` prototypes:

```zax
operator literal 'x' final : (
  result : MyType
)(
  payload : String immutable readonly final &
) unbound = {
}
```

The owner supplies qualification but no receiver instance. The compiler-created
payload is an immutable value in a final place and is exposed only through
readonly access. `once` is absent: `final unbound` already supplies one fixed
receiverless implementation, so `once final unbound` would be the redundant
non-acknowledgeable intent error described above.

Maintainer review aligns an exact literal-family alias:

```zax
x :: alias operator literal MyType
```

The left name selects the unique literal family with that same name under the
target type or namespace, here `MyType.x`. A literal family contains exactly one
declaration; generic result specialization occurs within it rather than through
several result-only overload declarations. The alias therefore needs no
prototype preference and canonicalizes directly to the target declaration:

```zax
MyType.x'first' <|> x'second'
// Both prefixes denote the exact same literal declaration.
```

This is not equivalent to declaring another compatible literal function with
`= MyType.operator literal 'x'`. Such a declaration could reuse one minted
implementation, but it would create another visible declaration contract and
would fail exact declaration-identity merge.

Whether an alias may target an incomplete declaration is also open. A type-use
alias to an already resolved forwarded type appears coherent because the target
identity is fixed while layout remains pending. An alias must not cause an
unresolved target name to be guessed later from another scope.

#### What is explicitly not aliasing

These operations remain distinct:

- `reference : MyType & = value` creates a same-place reference binding with its
  own declaration stance; it does not add another type/namespace name;
- `Name :: identity ... type Existing` creates a new nominal identity;
- a compatible visible callable prototype creates a new outward declaration
  contract around one minted implementation;
- an import binding names a module root and is not a runtime module value; and
- a forward declaration creates one incomplete name/category anchor, not
  another name for a separate completed declaration.

### Rejected keyword and operator-form aliases

#### Keyword aliases

The latest maintainer input rejects both single and compound keyword aliasing.
There is no `Module.System.Keywords`, no import-order-dependent keyword catalog,
and no local replacement spelling for language grammar.

Postfix `_` is the current one-word escape. It suppresses a possible keyword
role without changing semantic spelling:

```zax
as_()
```

`as` and `as_` cannot declare two different names. The explicit `_` remains in
source presentation for forward-compatibility even when no keyword conflict
currently exists.

`bare{...}` is an aligned complete-source neutralizer, not a keyword alias. It
suppresses non-operator keyword roles inside one independently complete
expression or effective statement while creating no scope, runtime operation,
grouping node, or final precedence boundary. It does not make malformed source
valid or grant authority.

Neutralization is strict:

```zax
bare{ value as Payload immutable readonly final & }
// error unless the neutralized words form another valid non-keyword reading
```

`as`, `immutable`, `readonly`, and `final` do not regain keyword roles merely
because those roles would make the payload convenient. Nesting is intentionally
unavailable:

```zax
bare{ first bare{ second } }
// Inner bare has no keyword role; ordinary parsing rejects the resulting source.
```

The language does not register a special nested-`bare` diagnostic. Inside the
outer enclosure, `bare` is an ordinary neutralized word; the attached `{` and
any unresolved ordinary name produce the normal applicable source diagnostics.
An implementation may explain that neutralization caused the reading, but no
dedicated error category or priority is promised.

The contiguous outer `bare{` opener, tree transparency, one-complete-unit rule,
and absence of a new scope remain the candidate's source model. Formatting and
source-reflection representation still require integration in their owners.

#### Operator-form aliases

The latest notes also reject aliases such as:

```zax
add :: alias operator +
not :: alias operator !
ellipses :: alias operator ...
```

Such aliases do not merely name declarations. They create alternate source
forms, collide with ordinary names and operator phrases, need fixity and mixfix
rules, and can undermine the closed form/precedence catalog. They are not part
of the candidate language.

All user-defined nonliteral operators are receiver-owned. This applies to
symbolic, phrase, circumfix, call/index, and mixfix declarations. Importing or
exactly aliasing a type preserves its receiver-owned operations with that type
identity; no separate global operator import, exposure, re-export, or
`alias operator` mechanism exists. Generative imported types receive distinct
operator declarations with their distinct receiver identities, and
`compatible` posture does not widen discovery.

Language-provided protected operations remain language-owned. Literal operators
are the deliberate receiverless exception and retain the aligned name-like
qualification, exact alias, and forward forms above.

Removing global user operators leaves a concrete unavailable direction:

```zax
myInteger + myCustomValue
// MyCustomValue cannot contribute an operation because it is not the receiver.
```

Future partial-type work has a strong incentive to permit an appropriately
authorized extension to the left receiver type so this source can be defined.
That work must preserve protected signatures, receiver ownership, extension
authority, order-independent conflicts, and reproducible import surfaces. Until
partial work accepts such authority, the programmer must use a custom-receiver
operation, phrase, function, or another explicit formulation. This pressure
belongs in the indexed partial-type raw input.

### Forward declarations and completion

#### Core meaning

A forward declaration introduces one source-ordered name and declaration-category
anchor before its direct definition or exact alias is available:

```zax
OtherNode :: forward type

Node :: type {
  next : OtherNode *
}

OtherNode :: type {
  next : Node *
}
```

The later `OtherNode` definition completes the anchor and establishes its
canonical declaration identity. It does not shadow or redeclare another name.
Named self-reference needs no `forward`:

```zax
Node :: type {
  next : Node *
}
```

A forward's qualified path determines its declaration owner, not the physical
namespace containing the source:

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

  // PendingType alone is not declared in C.
}

namespace A.B {
  PendingType :: type {
  }
}
```

`Module.` is optional because ordinary root lookup resolves `A` to `Module.A`;
`Module.A.B.PendingType` states that root explicitly. Every containing
component must already exist as a namespace or matching namespace forward, and
the source must have declaration/reopening authority over the target. The
qualified forward creates no short alias in `C`.

A forward declaration:

- is visible only where its eventual declaration would be visible;
- grants no implicit import or source inclusion;
- supplies no body, value, initialization, layout, member set, or overload
  preference;
- lets an earlier source position recognize a valid root without searching
  later source for a possible declaration;
- never uses source order to win a collision or overload tie;
- fixes the root-name anchor selected by any pending path, even when an exact
  alias completion later establishes its canonical target;
- must receive exactly one matching completion; and
- is an error if a required completion or suffix is still missing when its
  dependency must be finalized.

The source parses ordinary assignment, member, and call syntax independently of
symbol resolution. `forward` controls early root recognition and pending
semantic validation; language documentation should not promise a particular
compiler pause, restart, indexing, or symbol-table algorithm.

#### Pending uses

Uses that need only known identity/category may proceed. Uses that require more
remain pending:

- pointers and references can often name an incomplete type;
- by-value storage waits for size, alignment, construction, and destruction;
- a member suffix waits for the completed member scope;
- callable selection waits for a complete callable prototype/overload set;
- a literal invocation waits for its result contract and compile-time body; and
- a global initializer cannot read a forwarded value before that value's
  lifetime has begun.

Dependency cycles are not all equivalent:

- recursive reference/pointer type graphs may be valid;
- direct by-value recursive layout is invalid;
- namespace paths can be mutually referenced while their declarations complete;
- callable recursion can be valid when exact prototypes are available;
- global initialization cycles remain lifecycle errors; and
- unbounded recursive module import is rejected, while the exact repeat key
  across source versions and injection environments remains dependency work.

The last rule is deliberately separate from initialization ordering. A diamond
is legal; a repeated dependency on one active import ancestry is an error once
future dependency work defines which resolved source/version/injection facts
constitute that repeat.

#### Category test

A source-level forward category need not provide enough information to type-check
the dependent operation immediately. Its first purpose is to distinguish a
valid promised root from an unrecognized name so errors do not wait for a search
through the remaining source base. Category-dependent checks may remain pending.

The aligned source categories are:

- `Name :: forward type`;
- `Name :: forward enum`;
- `name :: forward variable`, including callable families;
- `Name :: forward namespace`;
- `Name :: forward module`;
- `Name :: forward reshape`; and
- `x :: forward operator literal`.

A type identity can support indirection before layout. A namespace or module
root can hold pending qualified suffixes. `forward enum` preserves the more
specific enum category before backing/member completion, while enum members need
no separate forward because they remain pending suffixes below that anchor.

`forward reshape` categorizes a no-storage mapping, but no transformation can
run until its complete directional paths are known. `forward module` anchors a
future generative import root:

```zax
ThirdParty :: forward module
ThirdParty :: import Module.ThirdPartyModule
```

The import, including its injection body, establishes the module instance and
completes pending exports and suffixes.

Maintainer review aligns category-only variable forwarding at module/namespace
scope:

```zax
myGlobal :: forward variable

myGlobal = "foo"
// The root is valid. Assignment waits for the completed variable type.

myFooBar.hello()
// error: myFooBar has not been declared or forwarded.
```

The forward deliberately supplies no value type, qualification, lifetime,
initialization state, or callable contract. It validates only the root and
category; every dependent semantic question remains pending until completion.
Because functions are variables, the same form may forward one global function
or a complete polymorphic function family:

```zax
myFunc :: forward variable
```

Local lexical variable forwarding would create initialize-later and
definite-lifetime pressure that conflicts with current ordinary declaration
rules. Nothing aligned here introduces local variable forwarding.

#### Callables, operators, and literals

A global function needs no separate forward category because
`forward variable` anchors its function variable or polymorphic family.
Type-owned functions need only their type/namespace root anchored; member lookup
and selection remain pending within that owner.

Receiver-owned symbolic, phrase, circumfix, call/index, and mixfix operators need
no operator forward. Their receiver is the root anchor, and user-defined global
operators do not exist. Forwarding a recognized symbolic form such as `+`
without a receiver or prototype would identify no declaration.

An unqualified literal prefix is the exception because it is itself a name-like
root with no receiver:

```zax
x :: forward operator literal

value := x'payload'
// x is recognized; result/prototype/execution checks remain pending.
```

A type- or namespace-qualified literal can instead remain a suffix under its
known owner. A direct literal completion or the aligned exact alias:

```zax
x :: alias operator literal MyType
```

can complete the forwarded family. The family contains one declaration, so no
overload preference is hidden by the category-only forward.

#### Completion matching

Completion must match:

- the same owning scope and qualified path;
- the same ordinary name or structured operator/literal form;
- the promised declaration category;
- any type/prototype facts written on the forward;
- compatible visibility/export promises that do not retroactively expose prior
  source; and
- exactly one completion.

A forward may complete with either a direct declaration or an exact alias of
the promised category. `forward module` completes through one import. Partial
declarations add to an already completed owner and do not complete a forward.
An incomplete forward is an error at module finalization even when no dependent
operation forced earlier completion.

A matching forward remains legal when source order made it unnecessary, such as
when no use occurs before completion. Tooling may lint the redundancy. Exact
visibility strengthening and syntax remain part of the explicitly deferred
visibility/directive design; a completion may not contradict visibility facts
already stated by the forward.

### Module dependency and source-acquisition boundary

The maintainer notes retain first-class Git acquisition, branch/tag/revision
selection, API version requests, content hashes, visible local caches, and cache
refresh. These are important Zax goals, but most belong to build/dependency
design rather than the namespace mental model.

The language-visible boundary needed here is:

```text
dependency request
-> one resolved immutable source identity
-> one complete injection environment
-> one generative module instance with an isolated Module root
-> local import binding and any deliberate exposures
```

A branch, tag, or API version is not itself immutable identity. A moved tag or
advanced branch can resolve to different content. Durable builds therefore need
at least a resolved source identity and canonical content/tree digest, while a
floating selector remains update intent. A local cache slug or manual refresh
identifier is presentation/control metadata and must not silently change the
identity of a locked build.

Two imports remain distinct module and declaration identities even when every
source and injection input is equal. A compiler may reuse parsing, static
analysis, generated code, or another cache product only when that optimization
preserves the separate language identities and individualized module state.
Automatic cache reuse is not module sharing.

Explicit sharing is declaration-level. Injecting an exact alias to an already
imported namespace lets another generative module instance use that same
dependency identity and state. A future broader shared-module/global facility
needs separate pressure; equality of import requests must never create it
implicitly.

The proposal that a content hash be optional is unsafe as a durable source
identity unless another immutable revision/signature supplies equivalent
protection. "Hash the downloaded contents as is" also needs a canonical byte or
tree definition covering file modes, submodules, large-file content, generated
inputs, line endings, and path normalization. These are concrete requirements
for the existing build/dependency raw owner, not implementation mappings for a
namespace document.

The maintainer's "no precompiled module" goal means an imported Zax module is
available as Zax source under this model. Foreign static/shared libraries and
other-language bindings cross a separately named ABI/interop boundary; they are
not another module-import shortcut.

Generated `generated/forwards/<hash>.zax` and
`generated/exports/<hash>.zax` files from the legacy page should not become
programmer-visible language requirements. A tool may internally index
declarations or cache interface information, but source semantics must depend on
the module's declarations and export contract, not generated-file presence,
location, parse order, or whether those files were checked in.

### Static declaration availability

Ordinary resolution should continue to fail loudly for an unknown or ineligible
name:

```zax
value : Legacy.WChar
// error on a target where Legacy.WChar is unavailable
```

Platform-optional declarations create a different future need: compile-time
source must be able to ask whether one symbol-aware path is available without
first triggering the ordinary error. The query must preserve:

- target versus compiler-host context;
- unknown versus known-but-target-unavailable declarations where useful;
- visibility eligibility;
- no side effects or declarations from a rejected branch;
- source-aware refactoring rather than fragile string-only lookup; and
- no fallback to a different lexical root after a suffix fails.

Exact query/enclosure syntax belongs to compile-time/reflection work. This item
should establish only that import/export/alias/forward metadata gives that query
a deterministic declaration identity and availability answer. A transparent
alias to an unavailable declaration cannot exist as a successfully resolved
ordinary alias; a future conditional source mechanism must control whether the
alias declaration itself is formed.

### Diagnostics, tooling, cost, and source stability

Diagnostics should distinguish:

- unknown root name;
- inaccessible/ineligible declaration;
- ambiguous directly exposed names;
- same-scope redeclaration;
- direct injected declaration colliding with target-module source;
- namespace reopening versus collision with a different declaration kind;
- path suffix missing below a complete prefix;
- path suffix still pending below an incomplete prefix;
- forward category/prototype mismatch;
- duplicate or missing completion;
- use requiring unavailable layout, member set, body, or initialization;
- recursive or unbounded module import;
- legal declaration dependency cycle versus impossible layout or initialization
  cycle;
- exact alias versus compatible visible declaration;
- an alias-resolved property profile that an actual source cannot satisfy;
- redundant `once final unbound`;
- operator-form alias rejection versus exact operator exposure;
- keyword alias rejection and the available keyword-role escapes; and
- locked source identity mismatch versus cache corruption or floating-selector
  update.

Tools should be able to show:

- the written path;
- the lexical root selected;
- every alias/exposure edge;
- canonical declaration and module-instance identity;
- eligibility/export reason;
- forward/completion state;
- callable/operator discovery domain; and
- why two same-spelled paths coalesced by identity or remained ambiguous.

Programmer-visible costs include:

- module acquisition and source verification;
- parsing/analysis of imported source;
- direct-exposure collision and overload analysis;
- compile-time work needed to complete forwards and static availability;
- retained callable/default environments behind compatible contracts;
- module-global construction and destruction; and
- larger rebuild/cache invalidation surfaces from wildcard exposure,
  re-export, alias retargeting, or dependency updates.

Source-compatibility events include:

- retargeting a transparent or exact alias;
- adding/removing/renaming an export;
- changing a direct exposure into a named import or the reverse;
- adding a same-spelled exported declaration to a directly exposed module;
- changing visibility so another overload becomes eligible;
- changing a module selector's resolved immutable identity;
- changing an injection environment or its captured declaration identities;
- replacing two generative imports with an explicit shared injected namespace,
  or the reverse;
- changing namespace ownership or moving a declaration between module roots;
- changing a callable alias from exact declaration identity to compatible
  visible-prototype adaptation;
- adding/removing a forward contract when it changes which incomplete uses are
  legal; and
- changing source-file order when global initialization is observable.

No source, declaration, file, import, module, generation, or cache order may
silently settle these events.

### Preliminary evidence disposition

This is a preliminary reconstruction, not archival disposition or authorization
to edit the evidence sources.

#### Refreshed maintainer notes

- Keyword aliases, compound keyword replacement, and operator-form aliases are
  negative decisions in the latest input and supersede the positive legacy
  proposals.
- Postfix `_` is already current. Maintainer review aligns `bare{...}` as
  existing strict keyword-neutral source with intentionally unavailable nesting;
  current source owners and raw maturity must be reconciled during promotion.
- Type aliases, anonymous-type aliases, reshape aliases, concrete generic
  aliases, callable prototype aliases, and computed type aliases retain useful
  positive input at different maturity levels.
- `bound`/`unbound` prototype behavior, fixed implementation storage, `once`
  interaction, and the receiver-capture boundary are aligned locally; capture
  syntax and representation remain future function-composition work.
- `forward type`, `forward variable`, `forward namespace`, and
  `forward operator literal` retain aligned use cases; enum, reshape, and other
  category needs remain open.
- Module-local roots, ordered source selection, named/direct import forms,
  generative import instances, direct injection, terminating recursive-import
  behavior, multiple versions, first-class Git acquisition, visible immutable
  caches, and ABI separation retain intent.
- Branch/tag/version/hash/cache details move to build/dependency work except for
  the immutable source identity needed as one input to imports here.

#### Raw namespace/import/visibility input

- Exact literal declaration identity through qualified and unqualified aliases
  is a hard pressure on the candidate alias/exposure model.
- Expected result type does not discover a literal owner.
- Visibility remains eligibility before preference.
- No order-based ambiguity repair is retained.
- Literal alias and unqualified forward syntax are aligned. Import, export,
  re-export, visibility, and broader forwarding syntax remain open in this item.

#### Legacy alias page

- Keyword and compound keyword aliases are superseded by the latest rejection.
- Operator/expression spelling aliases are superseded by the latest rejection.
- Qualification-changing type aliases use the aligned explicit-property overlay
  model.
- Generic alias families remain future generic input.
- Implicit `alias type func` should be replaced by explicit
  `alias type type of func`.
- Anonymous type/function prototype targets remain useful evidence.

#### Legacy forward page

- One source-ordered category anchor, mandatory matching completion, pending
  uses, and final unresolved errors remain useful.
- Category-only type, enum, variable/function, namespace, module, reshape, and
  literal-prefix forwards remain useful. Symbolic operator forwards are
  unnecessary under aligned receiver ownership.
- Phrase forwarding is superseded by current phrase design.
- Compiler pass/restart descriptions are implementation evidence, not
  programmer-facing semantics.

#### Legacy namespacing/import page

- `module.zax`, ordered source selection, local import roots, direct exposure,
  explicit export/re-export, terminating recursive-import behavior, and importer
  injection remain useful intent.
- Automatic sharing of identical imports is superseded by generative module
  instances. Exact injected namespace aliases provide deliberate sharing.
- First-import/first-declaration-wins is superseded.
- Generated forward/export source files are implementation ideas, not a
  language contract.
- Automatic `System` import policy and exact export/compiler-directive syntax
  remain unresolved.
- The process-global reading of `Module` is superseded by the aligned protected
  module-local root.
- Importer injection is current candidate design here. Conditional availability
  probing, version negotiation, acquisition, and cache refresh retain future
  build/compile-time mechanics.
- Current nearest-root, fixed-suffix, pending-path, and shadow permission rules
  already supersede the legacy lookup wording where they differ.

### Remaining explicitly deferred refinements

- Exact `private`, `[[export]]`, selective export, and re-export directive
  syntax, including how an alias deliberately exports only its new spelling.
- Module-global construction, repeated import, destruction, and concurrency.
- Symbol-aware optional declaration queries.
- Reflection identity for alias/exposure/import edges.
- Exact source/package locking and canonical content identity.

### Adjacent findings deliberately deferred

These consequences are captured here but should move to the listed live owner
before this work item can eventually archive:

- Package acquisition, Git selectors, resolved revisions, content identity,
  cache integrity, wildcard source lists, generated inputs, durable locking, and
  cache reuse across semantically distinct generative module instances:
  [raw build and dependencies](../raw/build-and-dependencies.md).
- Global construction/source order, dependency cycles, individualized module
  state, exact injected state sharing, possible future broader shared state,
  reverse destruction, and `once` concurrency:
  [raw global and once lifetimes](../raw/global-and-once-lifetimes.md).
- Generic aliases, partially applied aliases, type-returning functions,
  concrete-at-alias-boundary processing, generic forwards, specialization
  identity, and qualifier-generic prototypes:
  [raw type parameters and generics](../raw/type-parameters-and-generics.md).
- Receiver application through the suggested
  `[&myType] >> myBoundFunc` form, value/reference capture, generated closed
  callable prototypes, lambda identity, lifetime, and the conclusion that
  lambda and composition work should be reviewed together:
  [raw function composition and chaining](../raw/function-composition-and-chaining.md).
- Exact callable prototype comparison, labels/defaults, generic versus concrete
  selection, bound/unbound receiver slots, and generated declarations:
  [raw callable selection](../raw/callable-selection.md).
- Optional declaration queries, static branch selection, target/host context,
  and resolution-order interaction:
  [raw compile-time execution](../raw/compile-time-execution.md).
- Alias/declaration identity, source spelling, availability, visibility, and
  exact literal-alias reflection:
  [raw reflection](../raw/reflection.md).
- Keyword-neutral source now has aligned existence, strict neutralization, and
  lexical non-recognition of nested openers. Its complete source integration,
  ordinary nested-source diagnostics, formatting, reflection, and operator/mixfix
  interaction must consume and retire the superseded maturity in
  [raw bare source](../raw/bare-source.md).
- External type completion, imported extensions, authority, and order-independent
  partial merging, including the strong incentive to permit an authorized
  left-receiver extension for `Integer + MyType` now that user-defined global
  operators are unavailable:
  [raw partial types](../raw/partial-types.md).
- Static/shared library calls, foreign-language bindings, module ABI, and
  precompiled interoperation:
  [raw interoperability](../raw/interop.md).

### Likely lasting owner boundaries

If the candidate survives review, the eventual documentation fit should test:

- one cohesive namespace/module/import owner for module-local roots, namespace
  identity/reopening, named imports, exposure, export/re-export, visibility, and
  recursive-import behavior and the dependency-identity deferral;
- declarations and bindings for the one ordinary identifier namespace,
  root/suffix lookup, shadow permission, forward integration, and the taxonomy
  of alias versus binding;
- identity types for transparent type aliases versus new identities;
- qualifiers, transfer stances, and structural compatibility for what a
  concrete type alias may abbreviate and how explicit property overlays resolve;
- function invocation for callable prototype aliases, exact function-value
  binding, compatible visible contracts, and minted implementation identity;
- operators and the operator catalog for universal user-defined receiver
  ownership, removal of global user operators, and the absence of alternate
  operator-form spelling;
- literal source and operators for exact literal-prefix alias identity, merge,
  join acknowledgement, and diagnostics;
- source structure and operator phrases for postfix `_`, rejection of keyword
  aliases, and aligned strict `bare{...}` behavior;
- lifetimes for module/global instance consequences; and
- build, compile-time, generic, reflection, partial, and interop raw owners for
  the explicit deferrals above.

That early map is tested and refined by the dry run below.

## Dispositions and promotion dry run

### Result: PASS

The pre-promotion documentation-fit dry run passes. The aligned findings have a
cohesive programmer-facing owner, bounded integration points in existing current
owners, live destinations for every deferred consequence, and an exact proposed
change set. No promotion, deletion, archival, staging, or acceptance is
authorized by this result.

The principal architecture finding is that namespace/module behavior cannot be
taught coherently as fragments inside declarations, identity types, operators,
and literals. A dedicated current concept owner is required. One new focused raw
destination is also required for exact export/private/compiler-directive syntax
that was deliberately deferred while its semantic baseline was aligned.

### Lasting ownership map

#### Namespace and module owner

A new `language/namespaces-and-modules.md` owns:

- the protected module-local `Module` root and absence of a universal absolute
  namespace;
- automatic injection of language-provided declarations, protected
  `Scalars`, and protected short built-in names;
- namespace declaration, qualified reopening, module/namespace-only placement,
  owner-authorized contributions, and opening-local self-name `shadowable`;
- ordered source files sharing one module root without becoming namespaces;
- unqualified outward lookup as namespaces experience it, with a handoff to the
  general declaration algorithm;
- named imports, direct exposure, generative module-instance and declaration
  identity, import injection, exact injected sharing, and terminating recursive
  import with deferred repeat identity;
- collision provenance for direct declarations versus imported/exposed
  candidate sets;
- module-internal default visibility, explicit export, non-transitive imports,
  and explicit export of a named import binding at the currently aligned
  semantic depth;
- namespace/module costs, diagnostics, source stability, and the boundary to
  package acquisition, initialization, compile-time selection, partials, and
  reflection.

This owner teaches the feature from ordinary named imports and namespace source
before layering direct exposure, injection, forwarding, and deferred build
mechanics. It does not cite this work item or raw input.

#### Declarations and bindings

`language/declarations-and-bindings.md` remains the unique owner of:

- one ordinary identifier namespace per lexical scope;
- source-ordered root availability, nearest eligible-scope lookup, fixed roots,
  pending suffixes, and no suffix backtracking;
- general shadow permission, while linking the namespace-opening self-name rule
  to the namespace owner;
- the exact alias taxonomy and distinction from references, new identities, and
  compatible visible callable contracts;
- `alias variable`, including one varying slot and an approved polymorphic
  callable family;
- concrete alias-property overlay as a declaration operation, while qualifier,
  stance, and posture owners retain each axis's meaning;
- general forward anchoring, category matching, redundant-but-compatible
  forwards, mandatory completion, and the type/enum/variable/namespace/module/
  reshape/literal category list;
- function prototype `bound`/`unbound`, fixed implementation storage,
  receiver-slot presence, and the meaningful/error combinations with `once` and
  `final`.

Module imports, namespace syntax, and export behavior are linked rather than
redefined there.

#### Alias property owners

`language/identity-types.md` owns the fact that transparent type aliases preserve
canonical identity and create no overload domain or conversion.

`language/qualifiers.md`, `language/transfer-stances.md`, and
`language/structural-shapes-and-compatibility.md` retain the meanings and safety
constraints of the properties an alias may overlay. Each adds only the local
rule that a more local alias/use may replace an inherited property on its axis,
that omission inherits before common defaults apply, and that the resolved
profile cannot manufacture capability on an actual source.

`language/structural-shapes-and-compatibility.md` also owns exact `alias reshape`
identity and the local consequence of `forward reshape`.

#### Functions and invocation

`language/function-invocation.md` owns invocation of bound and unbound
prototypes, missing-receiver diagnostics, type- versus instance-qualified
`once bound` calls, and the distinction between a receiver-requiring prototype
and a closed captured callable.

`language/declarations-and-bindings.md` owns declaration/storage as above.
Future receiver capture, lambda generation, and composition remain in indexed
raw input rather than being implied by either current owner.

#### Operators and literals

`language/operators.md` owns universal receiver ownership for user-defined
nonliteral symbolic, phrase, circumfix, call/index, and mixfix operators; the
absence of global user operators; preservation of language-protected forms; and
the resulting unavailable `Integer + MyType` direction.

`language/operator-phrases.md` removes its global-symbolic workaround as current
behavior and points the unavailable left-intrinsic case to future authorized
partial extension pressure. `language/operator-catalog.md` retains form and
precedence ownership without becoming a declaration-location owner.

`language/literal-source-and-operators.md` owns the receiverless exception:
`final ... unbound` literal declarations, immutable/readonly/final borrowed
payload, unique literal families, exact `alias operator literal`, unqualified
literal forwarding, qualified pending suffixes, merge identity, visibility,
and diagnostics.

#### Source and keyword-neutral behavior

`language/source-structure.md` owns accepted contiguous `bare{...}` recognition,
one independently complete expression/effective statement, strict keyword
neutralization, tree/scope transparency, and ordinary failure of a nested
neutralized opener.

`language/operator-phrases.md`,
`language/intent-acknowledgements.md`, and
`language/operator-catalog.md` retain only their local enclosure/fence,
non-acknowledgement, and catalog consequences. `language/terms.md` owns concise
terminology.

#### Scalar and lifetime integration

`language/integers.md` replaces its future-visibility note with protected
automatic short-alias and `Scalars` routing to the namespace owner. Fixed and
floating owners already use the canonical `Scalars` paths without making a
competing visibility definition.

`language/lifetimes-and-references.md` keeps the current global/namespace
life-path boundary and links individualized module-instance initialization and
injected shared-state questions to future lifecycle work.

`language/enums.md` records `forward enum` and pending member suffixes locally.

### Explicit deferred destinations

- Create `project/raw/export-and-visibility-directives.md` for exact `private`,
  `[[export]]`, selective export, alias export, and re-export syntax. It inherits
  the aligned module-internal default, explicit-export requirement, and
  non-transitive baseline and must not reopen those semantics accidentally.
- Update `project/raw/build-and-dependencies.md` with first-class Git/source
  selection, immutable source identity, `module.zax` source ordering and
  wildcard pressure, injection-specialized compilation, visible cache integrity,
  import-cycle repeat identity across versions/configurations, and the rule that
  cache reuse never merges generative identities or state.
- Update `project/raw/global-and-once-lifetimes.md` with individualized module
  state, exact namespace injection as deliberate sharing, the absence of
  automatic super-module globals, and source/module construction consequences.
- Update `project/raw/type-parameters-and-generics.md` with concrete computed
  alias results, partially applied generic-alias deferral, alias property
  profiles, and generic forwarding.
- Update `project/raw/function-composition-and-chaining.md` with the suggested
  `[&myType] >> myBoundFunc` receiver application, value/reference capture,
  generated closed prototype, repeated invocation, and joint lambda/composition
  review.
- Update `project/raw/callable-selection.md` with bound/unbound receiver slots,
  `type of` without receiver capture, exact prototype selection versus selected
  declaration provenance, and polymorphic `alias variable`.
- Update `project/raw/compile-time-execution.md` with symbol-aware detection of
  injected declarations and conditional fallback import/source selection.
- Update `project/raw/reflection.md` with generative module-instance identity,
  namespace/alias/forward edges, bound versus captured receiver state, alias
  property overlays, and retained `bare{...}` source presentation.
- Update `project/raw/partial-types.md` with the removal of global user
  operators and the strong incentive for an authorized left-receiver extension
  to make `Integer + MyType` expressible while preserving protected signatures,
  ownership, conflicts, and order independence.
- Existing `project/raw/interop.md` already owns the deferred ABI/static/shared
  library boundary; no additional mechanism is created here.

### Structure and reading-path proposal

Retain the current `language/` directory and add only:

- `language/namespaces-and-modules.md` as one cohesive current owner; and
- `project/raw/export-and-visibility-directives.md` as one narrow temporary raw
  destination.

No new directory or specification area is needed.

`index.md` adds the namespace/module owner to both the direct-entry teaching list
and current conceptual-design list. It removes ordinary reader routes to the
legacy alias, forward, and namespacing pages once their useful content has moved.
The new owner links only to current domain owners. Raw files remain reachable
only through `project/raw/README.md` and project records.

The teaching order in the new owner is:

1. named namespace and named module-import examples;
2. module-local root, namespace identity, and source-ordered availability;
3. namespace declaration/reopening, owner authority, shadowing, and lookup;
4. generative imports, injection, exact sharing, and collisions;
5. visibility/export semantic baseline;
6. exact namespace/module alias and forward handoffs;
7. diagnostics, costs, source stability, and explicit future boundaries.

This gives a cold reader one complete model rather than requiring reconstruction
across declarations, literals, scalar catalogs, and legacy build notes.

### Exact proposed promotion change set

Add:

- `language/namespaces-and-modules.md`
- `project/raw/export-and-visibility-directives.md`

Modify current language owners:

- `language/declarations-and-bindings.md`
- `language/identity-types.md`
- `language/qualifiers.md`
- `language/transfer-stances.md`
- `language/structural-shapes-and-compatibility.md`
- `language/function-invocation.md`
- `language/operators.md`
- `language/operator-phrases.md`
- `language/operator-catalog.md`
- `language/literal-source-and-operators.md`
- `language/source-structure.md`
- `language/intent-acknowledgements.md`
- `language/integers.md`
- `language/enums.md`
- `language/lifetimes-and-references.md`
- `language/terms.md`

Modify public legacy/router surfaces:

- `index.md`
- `basics.md`
- `functions.md`
- `partial.md`
- `compiler-directives.md`

Modify live raw destinations and routing:

- `project/raw/README.md`
- `project/raw/build-and-dependencies.md`
- `project/raw/global-and-once-lifetimes.md`
- `project/raw/type-parameters-and-generics.md`
- `project/raw/function-composition-and-chaining.md`
- `project/raw/callable-selection.md`
- `project/raw/compile-time-execution.md`
- `project/raw/reflection.md`
- `project/raw/partial-types.md`

Update this active work record after promotion with applied disposition and
validation results:

- `project/work/024-namespaces-imports-aliases-and-forwarding.md`

No implementation source, operating-prompt source, project router, archive, or
unrelated language owner belongs to the promotion set.

### Legacy/raw retirement and archive effects

The active file's immutable reading scope still links the consumed legacy and
raw inputs. Promotion therefore removes them from ordinary public/raw routing
but does not delete them while this work item remains live.

After successful promotion and validation, the separately authorized closure
change set may:

- delete `alias.md`, `forward.md`, and `namespacing.md`;
- delete `project/raw/namespace-aliasing-forwarding-maintainer.md`,
  `project/raw/namespaces-imports-and-visibility.md`, and
  `project/raw/bare-source.md`;
- remove those raw entries from `project/raw/README.md`;
- archive this work file, update `project/README.md`, and create the separately
  aligned work item `025`.

The export/directive remainder moves first to the new indexed raw destination;
bare-source reflection remainder moves first to `project/raw/reflection.md`;
all other useful evidence moves to the owners listed above. Archived historical
links may require repository history as allowed by documentation guidance.

### Promotion validation plan

Promotion must verify:

- each aligned rule appears in exactly one complete owner and local links do not
  duplicate authority;
- the new owner opens with usable source and layers advanced injection,
  collision, and source-stability behavior after prerequisites;
- no current page calls `bare{...}`, literal aliases, bound/unbound prototypes,
  global user operators, module sharing, or import-order preference future or
  current contrary to the aligned model;
- no current literal example retains `final once` or a mutable/by-value payload;
- no current operator page retains a visible-global user declaration domain;
- scalar short-name visibility and protected `Scalars` agree across direct and
  qualified entry paths;
- source order affects declaration/forward availability but never ambiguity
  preference;
- generative module identities, injection ownership, and exact sharing agree in
  namespace, declaration, identity, literal, reflection-pressure, and lifetime
  text;
- every deferred item has one live indexed raw destination;
- live links and headings resolve, Markdown renders, and current pages do not
  cite numbered or raw project records;
- the consumed legacy/raw files retain no unique useful input before closure;
  and
- the staged maintainer-note and prior-working-record boundary remains
  untouched until separately authorized.

### Promotion application and validation

The language maintainer authorized the recorded promotion set after the PASS.
The promotion has been applied without staging, archival, deletion, branch
changes, or edits to operating-prompt sources.

Applied ownership changes:

- created `language/namespaces-and-modules.md` as the cohesive current owner;
- integrated lexical lookup, alias overlays, exact variable aliases, general
  forwards, and bound/unbound declarations into declarations and bindings;
- integrated alias property axes into identity, qualifier, transfer, and
  structural owners;
- integrated bound/unbound invocation, receiver capture boundaries, universal
  user-operator receiver ownership, literal aliases/forwards, strict
  `bare{...}`, scalar root visibility, enum forwarding, and module-instance
  lifetime pressure into their current owners;
- routed ordinary readers through `index.md` and removed its legacy alias,
  forward, and namespacing routes; and
- updated the affected legacy basics, function, partial, and
  compiler-directive pages by value.

Applied deferrals:

- created `project/raw/export-and-visibility-directives.md`;
- updated build/dependency, global/once lifetime, generic, function-composition,
  callable-selection, compile-time, reflection, and partial-type raw owners;
  and
- captured the strong partial-extension incentive for authorized
  `Integer + MyType` without restoring global operators.

The consumed alias, forward, namespacing, maintainer-note, namespace raw, and
bare-source raw files remain temporarily because this active work item's
immutable reading scope still links them. They have no ordinary public route and
are ready for the separately authorized closure retirement recorded above.

Validation passed:

- `git diff --check`;
- all relative paths and heading anchors across 97 live non-archive Markdown
  files;
- balanced Markdown code fences across the same live tree;
- targeted searches for stale current `final once` literal declarations,
  mutable/by-value literal payloads, visible-global user operator domains,
  future/unaccepted `bare{...}` wording, future literal alias/forward wording,
  obsolete system-type imports, and legacy public alias/forward/namespacing
  routes;
- current language owners contain no references to numbered work or raw project
  files; and
- the staged maintainer-note and pre-promotion working-record boundary remains
  unchanged, with the promotion itself entirely unstaged.

One unrelated live-link repair was included within the already authorized
function-invocation/reflection owner set: both references to the existing
`Recompose several results with -<` heading now use its rendered anchor.

Promotion validation therefore passes. Closure, retirement, work-item archival,
and creation of work item `025` remain separately unauthorized.

### Post-promotion maintainer review

Maintainer review produced corrections within the existing ownership map:

- removed every non-archive mention of an obsolete transfer spelling; ordinary
  behavior uses `copy` directly without teaching a discarded opposite to
  `deep`;
- retained `forward` as an incomplete declaration rather than a scoped
  unknown-name diagnostic suppression;
- added the qualified-forward rule and example: physical source may appear in
  another namespace, while the qualified path determines the owned declaration
  scope, every containing namespace must exist or be forwarded, and no local
  short alias is introduced;
- clarified that a wrong-category completion is an error and cannot trigger
  outward fallback;
- made the legal `Utilities` self-name forward/completion comments explicit;
- removed redundant maturity narration around `bare{...}`;
- narrowed the import-cycle statement to forbidden unbounded recursive
  expansion while assigning exact repeat identity across versions and injection
  environments to build/dependency work; and
- strengthened raw receiver-capture pressure by placing the missing-receiver
  error beside the suggested capture and successful closed call.

These corrections require no revised documentation-fit structure or additional
owner. The existing dry-run result remains PASS; the exact change set expands
only to the reached legacy `alias.md`, `casting.md`, and already reached
build/function-composition owners needed to apply the corrections by value.

The corrections have been applied and validated:

- every live non-archive transfer-stance use now uses the four current stance
  names directly;
- qualified forward, target-namespace ownership, required containing namespace,
  no implicit local alias, and wrong-category behavior agree in the declaration
  and namespace/module owners;
- import-cycle wording defers repeat identity consistently to build/dependency
  input;
- the raw receiver-capture example shows failed direct invocation and successful
  capture together;
- all relative links and heading anchors across 97 live non-archive Markdown
  files resolve;
- Markdown code fences are balanced; and
- `git diff --check` passes.

The maintainer staged the prior promotion boundary before these review
corrections. That review boundary was preserved through correction and
validation.

### Closure

The language maintainer accepted the completed work, authorized closure,
archival, staging, commit, and push, and aligned work item `025` as arrays,
indexing, slices, and element places.

All useful alias, forward, namespacing, focused raw, and maintainer-note input is
promoted, rejected, or moved to a live indexed raw destination. The exhausted
legacy and raw inputs named in the dry run retire during this closure. Work item
`025` receives a new empty maintainer-note path that must be populated before
its analysis begins.
