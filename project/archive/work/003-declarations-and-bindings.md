# 003: Declarations, bindings, and assignment

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `003` |
| Created | 2026-08-20 |
| Completed | 2026-08-21 |
| Owns | The historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current declaration and binding design, formal grammar, compiler behavior, or current project guidance |

## Non-authority notice

This archived file is a historical collaborative working record. Its statements,
observations, proposals, and aligned findings remain non-authoritative. Current
declaration and binding design is owned by the promoted language document
identified in the closure result below.

## Fixed initiating input

This section records the information known and aligned when work item `003` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for how Zax introduces names and
distinguishes declaration, initialization, assignment, and build-time or
type-level binding.

The review must determine the intended roles and relationships of:

- `name : Type`;
- `name : Type = value`;
- `name := value`;
- assignment with `=`;
- declarations using `::`;
- declarations without an immediate value;
- scope entry and visibility of a newly declared name;
- repeated declaration, shadowing, and accidental assignment to the wrong name;
- declaration statements within the accepted statement and body model;
- documentation attachment to declarations; and
- diagnostics and formatting that make declaration intent visible.

Use the review to determine whether the proposed punctuation earns its
unfamiliarity and whether its forms remain understandable in ordinary code.

### Motivating pressure

Declarations occur throughout the legacy corpus, but their punctuation and
meaning remain unreviewed. Accepted source structure now explains how statements
and bodies are formed without defining what declaration statements mean.

Several later concerns cannot be reviewed coherently until declaration and
assignment are distinct:

- flow-control initialization;
- parameters and function results;
- type and member declarations;
- inference;
- build-time bindings;
- shadowing and scope;
- structural typing; and
- initialization and lifetime.

Promoting other concept pages first would risk treating legacy declaration
punctuation as accepted merely because examples use it.

### Known assumptions

- The current [language vision](../../../language/vision.md) is accepted
  foundational direction.
- [Zax source structure](../../../language/source-structure.md) is accepted
  conceptual design.
- Statement-level newlines, semicolon composition, blocks, clause scope, layout
  validation, and documentation attachment retain their accepted meanings.
- Existing declaration syntax is legacy design input rather than authority.
- The review should establish a programmer-facing model before formal grammar
  or compiler behavior.
- Familiarity, readability, visible intent, and explicit cost remain evaluation
  pressures.
- A declaration model may expose consequences for types, lifetimes, functions,
  and flow control without prematurely designing those entire concerns.

### Known inclusions

- The distinction among declaration, initialization, and later assignment.
- The conceptual role of `:`, `:=`, `::`, and `=`.
- Explicitly typed versus inferred declarations.
- Declarations with and without initial values.
- When a declared name becomes visible.
- The relationship between a declaration and its enclosing source scope.
- Whether declarations are only statements or may occur in other syntactic
  positions.
- Shadowing, redeclaration, and mistaken assignment at the depth needed to make
  the model safe and readable.
- Declaration formatting and mandatory layout consequences.
- Leading and trailing documentation attachment to declarations.
- Immediate diagnostic expectations.
- Candidate lasting owner and reading-path boundaries.

### Known starting boundaries

- Exact structural-type identity, equivalence, layout, conversion, and
  subtyping.
- The complete type-inference algorithm.
- Detailed function parameter, result, overload, and invocation behavior.
- Multiple-value declarations and destructuring unless they expose a
  contradiction in the singular model.
- Detailed mutability, `final`, constant, and build-time execution semantics.
- Allocation, construction, destruction, ownership, and lifetime strategies.
- Module export, import, visibility, and namespace resolution.
- Detailed flow-control header semantics, including acceptance or rejection of
  `;;`.
- Formal grammar, parser data structures, lowering, or implementation.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Whether `:` is best understood as type ascription, declaration, or part of a
  larger declaration form.
- Whether `:=` provides enough readability or inference value to justify a
  distinct operator.
- Whether `::` represents one coherent category or currently combines unrelated
  concepts.
- Whether declaration without initialization is ordinary, restricted, or
  explicitly unsafe.
- Whether `=` has the same role during initialization and later mutation.
- Whether a declaration's scope begins before or after its initializer is
  evaluated.
- Whether shadowing is prohibited, restricted, or allowed with mandatory
  explicitness.
- Whether declarations can appear wherever statements appear or only in
  selected statement positions.
- Whether type and value declarations belong in one lasting owner or eventually
  separate after sharing a common binding model.

### Initial stopping guidance

Stop when the work has:

- established an aligned programmer-facing model for declaration,
  initialization, and assignment;
- dispositioned `:`, `:=`, `::`, and `=` at the necessary conceptual depth;
- established when a declared name becomes visible;
- established the relationship with source scopes and statements;
- dispositioned declaration without initialization;
- addressed shadowing and redeclaration enough to prevent hidden ambiguity;
- identified immediate formatting and diagnostic consequences;
- captured constraints on functions, types, flow headers, lifetimes, and
  structural typing without designing them prematurely;
- identified one plausible lasting owner and its boundaries; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `004` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides the accepted
  readability, familiarity, visibility, and explicit-cost principles.
- [Zax source structure](../../../language/source-structure.md) - provides the
  accepted statement, body, scope-visible, layout, and documentation framework
  within which declarations must fit.
- [Basics: operators](../../../basics.md#operators) - preserves the focused legacy
  punctuation roles proposed for assignment and explicit declaration.
- [Basics: type declaration](../../../basics.md#type-declaration) - contains the
  remaining declaration forms and examples, including inferred declaration,
  that initiate this review.

### Consequence-driven

- Read a focused section of [scope](../../../scope.md) when declaration visibility,
  shadowing, or scope entry requires legacy evidence.
- Read focused [type-definition](../../../type-definition.md) material only when
  `::` or member declaration cannot be evaluated from the basics material.
- Read focused function material only when parameter or result declarations
  expose a constraint on the general declaration model.
- Read focused flow-control initialization examples only when deciding whether
  declarations can occupy special header positions.
- Read namespacing or module material only when file-level declarations differ
  materially from local declarations.
- Read mutability, construction, lifetime, or allocation material only when
  declaration without initialization cannot be dispositioned without it.
- Read the [raw structural-typing input](../../raw/structural-typing.md) only if
  declaration shape crosses into structural identity or equivalence.
- Propose an update or new raw input when a material finding must be preserved
  for future work. Discussion, alignment, and explicit edit authorization still
  apply.
- Inspect focused Git history only when the origin or former purpose of an
  unusual declaration choice becomes consequential.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` and `002`.

Audit-only material requires direct language-maintainer instruction or a
specific audit or recovery need.

## Working record

### Aligned findings after review

The language maintainer and agent aligned on the findings in this section for
the scope of work item `003`. They remain non-authoritative working findings
until a separately discussed, aligned, and authorized promotion incorporates
them into lasting owner documents.

#### Programmer-facing core

Zax keeps declaration, initialization, and later assignment visibly distinct:

| Form | Aligned programmer-facing meaning |
| --- | --- |
| `name : Type` | Declare `name` with an explicit type and perform the type's default initialization. |
| `name : Type = value` | Declare `name` with an explicit type and initialize or construct it directly from `value`. |
| `name := value` | Declare `name`, infer its type from `value`, and initialize it. |
| `name = value` | Invoke an assignment operator using an already declared destination. It never declares `name`. |
| `name : Type = ???` | Declare live storage for `name` while explicitly and unsafely bypassing ordinary value initialization and construction. |
| `name :: definition-form` | Introduce a non-value definition form such as a type, enum, alias, import, namespace, or forward declaration. |

A **declaration** introduces a name and determines what kind of entity it
denotes. **Initialization** establishes the first state of a declared value.
**Assignment** is an operator expression involving an already declared
destination. Name lookup therefore precedes assignment overload selection:

```zax
x = 1 // error if x is not already declared
```

An unresolved assignment target is an error, not an implicit local declaration.

`:=` is conceptually the adjacency of two operators:

```zax
x := 0
y : = 0
```

Both forms declare an inferred `Integer` and initialize it from `0`. A formatter
should emit the compact `:=` spelling consistently. A compiler may optimize
initialization without changing the programmer-visible distinction between
declaration and initialization.

The initialized typed form performs direct initialization:

```zax
item : Item = source
```

It does not mean "default-initialize `item`, then assign `source`." That
interpretation could introduce an unintended constructor, destructor,
allocation, or other observable operation.

A bare declaration marker supplies neither a type nor an initializer and is an
error:

```zax
i: // error: no explicit or inferable type
```

Legacy flow examples such as `while i: ;; i < 100` are mistakes. Coherent forms
include:

```zax
while i := 0 ;; i < 100
    use(i)
```

```zax
while i : Integer ;; i < 100
    use(i)
```

The second form receives `Integer`'s default initialization.

#### Default and explicitly bypassed initialization

Every ordinary value declaration initializes its value. No type is exempt:

```zax
count : Integer
item : Item
```

Default initialization may execute constructors, initialize contained values,
allocate storage, or perform other visible work. Constructors and destructors do
not return error results and Zax does not use exceptions, but initialization may
still encounter a panic such as allocation failure or stack exhaustion.

`???` is the explicit unsafe escape from ordinary initialization:

```zax
item : Item = ???
```

This form:

1. reserves storage;
2. establishes a live variable of the declared type;
3. skips ordinary value initialization and constructor execution;
4. leaves the representation indeterminate;
5. schedules the ordinary destructor at scope exit; and
6. makes the programmer responsible for establishing every invariant required
   by later use and destruction.

This permits intended low-level use:

```zax
if condition {
    item : Item = ???
    initializeThroughAssembly(item)
} // Item's destructor runs
```

If the low-level operation does not leave `item` in a state accepted by later
operations and its destructor, the programmer invoked unsafe behavior and owns
the consequences. The compiler should diagnose obvious misuse when practical,
but incomplete analysis does not make the operation illegal.

A declaration without an explicit initializer and a later assignment are two
ordinary operations:

```zax
x : Integer; x = 1
```

This default-initializes `x` and then invokes assignment. It is equivalent in
those respects to:

```zax
x : Integer
x = 1
```

It is not an initialize-later construct.

#### Inference

Inferred declarations derive their type from the initializer and its immediate
context. They do not scan later statements to discover intended use:

```zax
value := makeValue()
```

Some type-shape or policy qualifiers may eventually be written while the
remaining type is inferred:

```zax
reference : & = original
pointer : * = original
```

The exact inferable qualifiers and inference algorithm remain deferred, but the
declaration model must permit this partial explicitness without requiring the
entire type to be named.

#### Visibility and initialization

An ordinary value binding enters scope only after its initialization completes.
The new name does not participate in lookup while its type and initializer are
evaluated:

```zax
x := use(x) // error if no outer x exists
```

If an outer `x` exists and permits shadowing, the right-hand `x` resolves to that
outer binding. The new inner `x` becomes visible only afterward.

Same-scope redeclaration is always an error:

```zax
x : Integer
x := 1 // error: x is already declared in this scope
```

Semicolon composition does not create a scope:

```zax
x := 1; x := 2 // error: same-scope redeclaration
```

Earlier operands may introduce names for later operands:

```zax
x := makeValue(); y := use(x)
```

This agrees with the accepted source model in which composed operands share the
enclosing clause scope.

#### Deferred function bodies and recursive bindings

A function body is delayed code rather than an expression executed while the
function value is initialized. A function body may therefore refer to its own
completed binding:

```zax
factorial : (result : Integer)(n : Integer) = {
    return n <= 1 ?? 1 ;; n * factorial(n - 1)
}
```

This is a narrow recursive-binding rule, not permission for an ordinary
initializer to self-reference. Ordinary surrounding captures are discovered
lexically and materialized when the function value is constructed. A function's
self-reference cannot be an ordinary by-value copy of a function value that does
not yet exist; it is a recursive binding edge resolved for the delayed body.

Whether recursive and mutually recursive functions require explicit `forward`,
and whether a non-`final` self-call follows the current binding after
reassignment or the originally created function value, remain deferred to
function, capture, and forward design.

#### Shadowing

Same-scope redeclaration is distinct from nested shadowing. Nested shadowing is
prohibited by default. The outer declaration that may be hidden must explicitly
grant permission:

```zax
value shadowable := 1

if condition {
    value := 2 // legal
}
```

`shadowable` describes the outer binding's willingness to be hidden. It is not a
marker on an inner declaration requesting permission after the fact. The newly
declared inner binding is not automatically `shadowable`:

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

The rule applies generally to ordinary lexical declarations that can be
shadowed, including value, type, alias, import, and namespace names. Exact
treatment of overload groups and labels remains later category-specific work.
A `forward` declaration and its later completion are one declaration, not
shadowing.

Once a name is shadowed, ordinary lookup no longer reaches it. Zax does not add a
general parent-scope or `..` operator merely for this case. A programmer who
needs both names can establish an alias before shadowing.

`shadowable` is the leading keyword because it is one word, readily understood,
and accurately describes the outer declaration. Keyword selection generally
prefers:

- one word where practical;
- established English where practical;
- otherwise a readily understandable English derivation;
- no abbreviations unless unavoidable;
- no negated, hyphenated, or underscore-dependent compounds when a clearer word
  exists; and
- semantic attachment and direction over superficial familiarity.

#### Explicit instance-member access

Instance members are not injected into an instance function's ordinary lexical
lookup. Access through the current instance must use `_` explicitly:

```zax
value := 0

value = 1
_.value = 2
_.foobar = 3
foobar = 4 // error: no local foobar is declared
```

This prevents copied code, a newly added member, or a forgotten local declaration
from silently changing name resolution. The same explicit-self principle applies
to instance member functions. Static or shared members use their appropriate
qualified form.

#### One lexical identifier namespace and qualified paths

Each lexical scope has one ordinary identifier namespace. This avoids
context-dependent type-versus-value lookup and makes collisions visible.
Structured cases remain distinguishable:

- declarations with the same name may form one overload set when overload rules
  permit it;
- a forward declaration and its completion are one declaration;
- members occupy their containing entity's member scope;
- operators are not ordinary identifiers; and
- labels may require later control-flow-specific treatment.

Type-like and value-like declarations have mandatory capitalization and casing
intent. For example:

```zax
myType :: type { } // error: type-like names begin as type names
MyValue := 0       // error: value-like names begin as value names
```

The compiler enforces mechanically knowable intent rules. Lints can advise on
acronyms, discouraged but legal shapes, and conventions that are not source
validity. Editors or spelling tools may offer dictionary and domain-language
assistance that a compiler cannot decide reliably.

Qualified paths resolve deterministically:

```zax
value : Node.Node
```

The first component is resolved lexically once. Every remaining component is
resolved only inside the entity selected by its preceding component. Failure to
resolve a later component is an error; lookup does not backtrack to another
outer candidate for the first component.

Resolution of a suffix may remain pending when an already resolved prefix names
an incomplete declaration:

```zax
MyType :: forward type

value : MyType.SubType
```

`MyType` resolves immediately to the forwarded declaration and that choice is
fixed. If completion of `MyType` later discovers `SubType`, the compiler resolves
the pending suffix against that completed declaration. If `SubType` never
appears, the path is unresolved when the dependency graph must be finalized.
The compiler does not retry another outer `MyType`.

An explicit nested forward may categorize a needed member earlier:

```zax
MyType :: forward type
MyType.SubType :: forward type
```

It is not required merely because the containing type was incomplete when the
path was first encountered. Operations that require the completed size, layout,
overload set, or initialization behavior remain pending until enough information
is available. Later binding permits dependency-directed resolution without
permitting speculative rebinding.

Aliases and stable roots preserve access when ordinary names would be hidden:

```zax
OuterNode :: alias type Node
value : OuterNode.Node
```

```zax
value : Module.Node.Node
```

Imports can similarly use aliases. A path does not excuse a collision in its
root name, and an importer must not silently guess which root was intended.

#### Qualifier and capability axes

Declaration review exposed three independent programmer-facing axes:

| Pair | Governs |
| --- | --- |
| `final` / `varying` | Whether a storage place may be replaced |
| `mutable` / `immutable` | Whether the underlying value may ever change |
| `writable` / `readonly` | Whether the current access path may modify the value |

The distinctions are:

- mutable: the underlying value is permitted to change;
- immutable: the underlying value is guaranteed not to change;
- writable: this access path may change the value; and
- readonly: this access path may observe but not change the value.

Thus mutable means someone may change the value, writable means this access may
change it, readonly means this access may not change it, and immutable means no
access may change it.

`readonly` replaces legacy `constant` as the access-path promise. It is an
established programming-language token, pairs clearly with `writable`, and
avoids confusion between read-only access and compile-time constant evaluation.
Compile-time constants and compound operations such as `is constant` retain
their separate meanings.

`writable` may remain the ordinary unqualified access default and appear
explicitly only where a positive capability must be stated, selected, reflected,
or restored. Exact syntax is deferred.

`final` and `varying` can qualify both a declared variable and a place exposed
through a type use. They always apply to the immediately qualified storage
place:

```zax
alias final : Foo varying &
```

The `alias` binding cannot be rebound, while the referenced `Foo` place may be
replaced.

```zax
alias varying : Foo final &
```

The alias may be rebound, while the referenced place may not be replaced.

A directly stored value and its variable occupy one replacement boundary.
Compatible explicit qualifiers on that boundary combine idempotently:

```zax
foo final : Foo final = makeFoo()   // legal: effective stance is final
foo varying : Foo varying = makeFoo() // legal: effective stance is varying
```

Conflicting explicit qualifiers are errors:

```zax
foo final : Foo varying = makeFoo() // error
foo varying : Foo final = makeFoo() // error
```

An omitted qualifier supplies the default only when no explicit qualifier
establishes another stance:

```zax
foo : Foo         // varying by default
foo : Foo final   // final
foo final : Foo   // final
```

Visible compatible repetition may receive optional lint guidance, but it is not
a language error. This supports aliases, generic substitution, generated
declarations, and deliberate explicitness without making compatible qualifiers
fragile.

A `final` place is nonreplaceable, not immutable:

```zax
foo final : Bar = makeBar()

foo = other       // ordinary replacement candidate is unavailable
foo.member = 200  // legal when Bar and this access are mutable and writable
```

The place capability survives references and captures. A reference derived from
`foo` cannot silently regain replacement permission. Copying by value creates a
new place with its own declaration stance.

Capability conversions may safely remove permissions or request weaker access:

- immutable values may provide readonly access;
- writable access may be viewed as readonly;
- varying places may be viewed as final;
- final places may not become varying through an ordinary conversion;
- readonly access may not become writable through an ordinary conversion; and
- a mutable or readonly value does not thereby become immutable.

The complete qualifier grammar and cast lattice remain deferred.

`move` and `copy` are contextual transfer operations, not persistent
`movable`, `immovable`, `copyable`, or `noncopyable` qualifiers. Whether a
particular transfer is available depends on the complete source, destination,
qualifier, policy, and overload context. Ordinary reference passing aliases a
value and is neither a move nor a copy.

Work item `003` does not classify broader capabilities such as sendability,
synchronization safety, pinning, known layout, copying, or interior freezing.
Future reviews may represent them as qualifiers, selectable operations,
reflection, concepts, compiler-recognized guarantees, type-definition contracts,
or combinations of these. The present declaration model neither requires nor
precludes any of those dispositions.

#### Assignment and overload selection

An overloadable operator is an ordinary selectable function. User-defined
operators may have domain-specific effects and results that are unusual outside
their domain. The language does not infer declaration from those effects.

For:

```zax
consume(a = 1)
```

the compiler:

1. resolves `a`;
2. selects a viable `=` candidate for the qualified operands;
3. invokes it; and
4. maps its result to `consume` if the result shape is accepted.

The selected operator determines its effects and results. Exact built-in return
behavior and complete overload resolution remain later operator work.

No overloadable operator receives implicit access that its signature does not
request. Generated defaults are ordinary candidates with declared capability
requirements. A normal replacement candidate requires a varying destination and
therefore is unavailable for a final place. A domain-specific `=` candidate that
accepts a final or readonly left operand may remain selectable because the token
itself is not banned:

```zax
foo final : Bar = makeBar()
result := foo = other // legal only if a matching non-replacement candidate exists
```

Compound assignment, increment, decrement, and other operators likewise require
already declared operands and use ordinary selection. They never introduce
bindings.

Declaration initialization remains reserved:

```zax
foo : Bar = source
```

The `:` introduces `foo`; user code cannot overload that act. The initializer
selects construction or initialization behavior for the new `Bar`. Later
`foo = source` performs operator selection against an existing destination.

#### Non-value definitions, `forward`, and `Self`

`::` introduces a family of non-value definition forms:

```zax
Point :: type { }
Fruit :: enum { }
FriendlyName :: alias type ExistingType
ModuleName :: import Module.Definition
TypeName :: forward type
```

The family need not imply one runtime behavior. In particular, `::` does not mean
"evaluated at build time." Build-time versus runtime evaluation is an orthogonal
property of functions, inputs, directives, and evaluation context. Build-time
values continue to use ordinary `:` or `:=` bindings.

Functions and ordinary values use `:`, including anonymous types:

```zax
callback final : FunctionType = { }
value final : :: type {
    member : Integer
}
```

Legacy `final ::` function examples are documentation errors.

Named recursive types use explicit `forward` in the baseline:

```zax
Node :: forward type

Node :: type {
    next : Node *
}
```

The later definition completes the forwarded declaration. Direct infinitely
recursive layout remains an error:

```zax
Node :: forward type

Node :: type {
    next : Node // error: infinitely recursive layout
}
```

Mutually recursive named types can forward each required name.

`Self` supports self-reference when no declared type name exists:

```zax
value : :: type {
    next : Self *
}
```

`Self` always denotes the innermost enclosing type. It may also be used where a
named type prefers explicit innermost-type intent. Explicit `forward` remains
available for named self-reference and mutual recursion. Automatically making a
type's own unforwarded name incomplete inside its body is not part of the
baseline; later compiler maturity and concrete pressure may reconsider it.

#### Declaration positions and flow-control initialization

The binding model applies beyond local declaration statements. Parameters,
results, captures, stored members, type names, imports, and flow headers provide
their own declaration contexts. They share name, type, initialization, and
qualifier concepts without all becoming statements.

A flow-control initializer accepts any effective statement. A declaration in
the header becomes visible to the condition, every applicable clause body, and
the corresponding false or `else` clauses, then leaves scope after the complete
flow statement:

```zax
if result := tryValue() ;; result.isValid()
    use(result)
```

An explicit block inside a composed initializer retains its own nested scope:

```zax
if {
    token := hello()
    goodbye(token)
}; value := compounding() ;; value < 1 {
    use(value)
    token = 5 // error: token belonged to the completed inner block
}
```

This unusual form is legal absent another concrete pressure. Exact `;;` and
flow-header layout rules remain deferred to flow-control and source-structure
work.

Stored-member initializers run once per actual containing instance. A storage
qualifier such as legacy `once` may change how many storage instances exist and
when the one instance is initialized; it does not make an ordinary member
initializer run once merely because it appears once in a type definition.

Default parameter expressions are later function design. The desired concept is
that an explicit argument or the default expression initializes the parameter,
not both. Declaration design does not change merely to preserve a speculative
`=` spelling for defaults.

#### Multiple results and documentation

Contextual multiple-result declarations may share one initializer:

```zax
first:, second: = produceTwo()
```

Each `name:` explicitly declares a binding. Result mapping must be unambiguous;
duplicate names remain errors. Optional results, discarded results, construction
order, and cleanup after panic remain function and lifetime work.

Documentation attaches to the complete declaration construct:

```zax
/// Describes x.
x := makeValue()
```

```zax
/// Describes the produced pair.
first:, second: = produceTwo()
```

The second block documents the complete multi-binding declaration rather than
only its leftmost name. Documentation before a flow statement attaches to the
flow statement under the accepted source-structure rules, not automatically to
a declaration inside its header.

#### Reviewing legacy examples

A code fragment need not declare every name locally to illustrate assignment:

```zax
x = y
```

This may intentionally mean "assume `x` and `y` already exist; invoke
assignment." Changing it to `x := y` would change the example from assignment to
declaration and initialization.

Example corrections must follow the stated or strongly evidenced intent:

- preserve `=` when prose or context discusses assignment, reassignment,
  replacement, update, mutation, or assignment-operator selection;
- use `:=` when the example introduces a new inferred binding;
- use `name : Type = value` when explicit type, conversion, construction, or
  type-directed initialization is part of the point;
- add the smallest useful declaration or comment when omitted context makes the
  intended assignment unnecessarily confusing;
- record an ambiguous example rather than silently deciding its semantics; and
- defer correction when the right form depends on an unreviewed concern.

Review associated with work item `003` should correct declaration punctuation in
examples directly relevant to these findings. It should not proactively rewrite
unrelated legacy snippets merely to make every fragment independently
compilable. The test is whether a change clarifies the example's existing intent
rather than choosing new behavior for it.

#### Immediate diagnostics and formatting

Without choosing formal diagnostic identifiers, the model requires diagnostics
that distinguish:

- assignment to an unresolved name;
- a declaration with neither an explicit nor inferable type;
- same-scope redeclaration;
- shadowing without permission from the hidden declaration;
- use of an ordinary binding before initialization completes;
- conflicting explicit place qualifiers;
- a type mismatch or unavailable initializer;
- an unavailable operator candidate for the qualified operands;
- an attempt to regain replacement, writable, or mutability capabilities not
  supplied by the source;
- use of `final ::` for an ordinary function or value;
- direct infinitely recursive type layout;
- use of an incomplete type where completed layout is required;
- ambiguous multi-result mapping;
- implicit instance-member access without `_.`; and
- a declaration form used in a source position that does not accept it.

Formatters should make declaration intent obvious:

```zax
inferred := value
explicit : Type = value
stableValue final : Type = value
anonymous final : :: type { }
```

Formatting should preserve the separation among binding, value, access, and
referent-place qualifiers. A formatter may canonicalize `: =` to `:=` but must
not silently resolve contradictory source intent.

#### Explicit deferrals and preserved constraints

The following questions remain explicitly deferred. None invalidates the aligned
declaration baseline:

- the syntax and analysis contract by which a low-level function accepts
  uninitialized storage and proves or asserts that it established a valid value;
- complete definite-initialization analysis for `???`, including diagnostics,
  unsafe boundaries, return-value optimization, relocation, partial
  construction, and destruction paths;
- exact capture representation, recursive-function `forward` requirements,
  mutually recursive functions, and non-`final` recursive-call behavior;
- the complete inference algorithm and which type-shape, ownership, lifetime, or
  policy qualifiers may be partially explicit;
- the complete qualifier grammar, capability defaults, casts, references,
  captures, and whether move consumption requires a separately expressed access
  capability;
- move and copy selection, source-state consequences, and ownership behavior;
- exact built-in operator results, assignment-expression value categories,
  overload resolution, conversion ranking, and generated operator sets;
- default `Nothing` function behavior beyond the aligned direction that obvious
  calls should be diagnosed, an otherwise unhandled call should panic, and
  future explicit directives may request `nothing=noop` or `nothing=default`;
- complete constructor, destructor, allocation, initialization-failure,
  lifetime, and panic cleanup behavior;
- default parameter syntax, evaluation timing, and argument mapping;
- complete multiple-result, optional-result, destructuring, ordering, and panic
  cleanup behavior;
- exact flow-header grammar, `;;`, and mandatory layout for unusual effective
  statements;
- complete overload-group, label, import-collision, and namespace-category rules
  within the one-identifier-namespace model;
- the formal grammar and exact common taxonomy of `::` definition forms;
- broader type capabilities such as sendability, synchronization, pinning,
  layout knowledge, copying, and interior freezing; and
- structural-type identity or equivalence consequences of member declaration
  names, qualifiers, defaults, and inferred types.

### Superseded initial reconstruction

The following initial reconstruction and question set is retained as discussion
history. The aligned findings above supersede its candidate baselines, open
questions, and tentative recommendations.

#### Evidence posture

The material below is an initial reconstruction for review, not an aligned
language decision. The accepted source-structure model constrains where
declarations can appear as statements, how they compose, which scope contains
them, how their layout is validated, and how documentation attaches. It does not
choose declaration syntax or initialization behavior.

The legacy pages are broadly consistent about one essential distinction:

```zax
x = 1   // assign to an existing x
y := 1  // declare y, infer its type, and initialize it
```

An unqualified `=` does not appear intended to create its left-hand name.
Examples that rely on that interpretation should therefore be treated as likely
mistakes unless stronger evidence establishes a special context.

#### Candidate reconstruction of the core forms

The strongest model recoverable from the current evidence is that Zax has a
visible declaration marker and keeps name introduction distinct from later
mutation:

| Form | Candidate programmer-facing meaning | Evidence and qualification |
| --- | --- | --- |
| `name : Type` | Declare `name` with an explicit type and no explicit initializer. | Legacy type and local-variable examples say the type supplies default initialization. Omission is therefore not presently the same as leaving storage uninitialized. |
| `name : Type = value` | Declare `name` with an explicit type and initialize it from `value`. | The `:` makes name creation explicit. The `=` introduces the initial value in this declaration context; it does not imply that bare assignment can declare a name. Constructor selection or conversion may occur, but those rules belong elsewhere. |
| `name := value` | Declare `name`, infer its type from the initializing context, and initialize it. | The legacy basics page explicitly says `:` and `=` are two distinct operators in this spelling. Regardless of eventual tokenization, programmers can understand the compact form as an inferred initialized declaration, not assignment with implicit declaration. |
| `name = value` | Assign to an already declared, writable destination. | Name lookup must succeed independently of assignment. Failure to find `name` is an error, not an invitation to create it. |
| `name :: declaration-form` | Introduce a type-level or namespace-level declaration such as a type, enum, alias, or named import. | The evidence does not support treating `::` as ordinary value declaration or as a general synonym for "evaluated at build time." It may instead introduce a family of declaration-producing forms whose exact common semantics remain to be found. |

This reconstruction makes **declaration**, **initialization**, and **assignment**
separate concepts:

- A declaration introduces a name and determines what kind of entity the name
  denotes.
- Initialization establishes the first usable state of a declared value.
- Assignment changes an existing destination after its declaration. It never
  creates a fallback declaration.

The punctuation can compose those concepts without collapsing them. In
particular, `name : Type = value` is one initialized declaration even though its
initializer uses the same visible `=` character as later assignment. The
declaration marker is what makes creation unambiguous.

#### What the legacy corpus appears to imply

##### `:` is broader than a local-variable statement

The `name : Type` shape appears for locals, stored members, function values,
parameters, results, captures, and other binding positions. A local declaration
can be a simple statement, but declarations as a language concept cannot be
limited to statement positions. Parameter lists and result lists, for example,
provide declaration contexts of their own.

Function declarations reinforce the value-binding interpretation:

```zax
double : (output : Integer)(input : Integer) = {
    return input * 2
}
```

Here `double` is a declared name with a function type and an initial function
value. Parameters and results use typed binding syntax within that type.
Whether all such forms share exactly one grammar production is a later formal
question; their programmer-facing concepts should still agree.

##### `:=` appears to be a compact inferred declaration

The legacy explanation does not describe `:=` as an assignment operator that
happens to declare. It describes adjacent `:` and `=` roles: declaration with an
omitted explicit type, followed by initialization from which the type is
deduced. This also explains contextual multi-result examples such as:

```zax
grams:, ounces: = weigh("letter")
```

Each `name:` marks a new result binding and the shared initializer supplies the
corresponding result types and values. This is useful evidence about the model,
but multiple-result declaration remains a deliberately bounded later concern.

The corpus also contains bare forms such as `while i: ;; i < 100`. No type or
value is available there from which `i` can be inferred. Unless that header has
an unstated contextual source, these examples conflict with the otherwise
coherent model and are likely incomplete or mistaken.

##### Omitted initialization currently means default initialization

Legacy type and construction material repeatedly treats:

```zax
value : Integer
instance : SomeType
```

as declarations whose values are initialized by the declared type's default
behavior. That behavior may be trivial zero/empty initialization or may involve
construction. The same material uses an explicit `???` initializer to request
uninitialized bits:

```zax
value : Integer = ???
```

The recoverable distinction is therefore:

- no explicit initializer: request the type's default initialization;
- `= value`: initialize from the supplied value or constructor input;
- `= ???`: explicitly opt out of ordinary initialization, subject to future
  safety and lifetime rules.

This is only recovered intent. Whether implicit default initialization hides too
much cost, which types can provide it, and whether `???` belongs in the eventual
language require later alignment.

##### `::` does not yet have one demonstrated semantic category

The recurring, apparently intentional uses are:

```zax
Point :: type { ... }
Fruit :: enum { ... }
FriendlyName :: alias type ExistingType
ModuleName :: import Module.Definition
:: import Module.System.Types
```

These forms create type-level declarations, aliases, or namespace effects rather
than ordinary mutable values. The unnamed import also shows that `::` is not
always a binary "bind this name" operation.

Other evidence argues against defining `::` merely as "compile-time binding."
Legacy module definitions use `final :=` for values consumed during build or
import processing, and compile-time parameters use ordinary `:` declarations
with directives. Execution phase therefore appears orthogonal to the
declaration punctuation.

A few pages use `final ::` for functions while the overwhelming function syntax
uses `final :`. No accompanying explanation establishes a distinct meaning.
Those occurrences should be treated as unresolved inconsistencies, not evidence
that functions generally use both forms.

An anonymous type example also composes the families:

```zax
value : :: type { ... }
```

This appears to declare a value with `:` whose type is an anonymous type
expression introduced by `:: type`. It is useful evidence that the two marks
have different jobs, but the anonymous-type form itself does not need resolution
in this work item.

##### Scope evidence is incomplete but consequential

The accepted source model already establishes that a declaration in a
flow-control body belongs to the clause scope, and that earlier operands of a
composed body can introduce names used by later operands. Legacy scope material
also assumes lexical lookup from the innermost scope outward and permits an
inner name to shadow an outer name.

Explicit declaration punctuation reduces one class of error: writing `x = value`
can never accidentally create a new local `x`. It does not by itself prevent:

- an assignment from resolving to an unintended existing inner name;
- an inferred declaration from accidentally shadowing an outer name;
- two declarations from competing in the same scope; or
- a declaration initializer from ambiguously referring to the new name or an
  outer name with the same spelling.

Those cases require explicit scope and diagnostic decisions.

#### Candidate semantic baseline for discussion

The following is a proposed baseline extracted from the evidence and the stated
goal of explicit declaration. Each point remains open to revision:

1. Every source-level name introduction must use a declaration form appropriate
   to its context. Assignment never implicitly introduces a name.
2. `name := value` is the ordinary inferred local declaration spelling.
   `name : Type = value` is its explicitly typed counterpart.
3. `name : Type` requests default initialization; it does not silently mean
   uninitialized storage.
4. In an ordinary value declaration, the new name should not participate in
   lookup while its type and initializer are being evaluated. Thus an inner
   `x := x + 1` would read an outer `x` if shadowing is legal, and would be an
   unresolved-name error if no outer `x` exists.
5. The binding should become usable only after successful initialization. This
   avoids exposing partially initialized ordinary locals and matches the legacy
   prohibition on control flow that bypasses initialization.
6. A repeated declaration in the same scope should be an error. Nested
   shadowing is demonstrably contemplated by the legacy design, but whether it is
   freely allowed, restricted, or requires an explicit marker remains open.
7. Local declarations are simple statements and participate in accepted
   semicolon composition. Other declaration contexts, such as parameter lists,
   member lists, and flow headers, reuse the binding concepts without becoming
   statements.
8. `::` should remain separate from the value declaration and assignment model
   until its type, alias, import, and possible build-time roles can be stated as
   one coherent concept. Stray `final ::` examples should not decide that model.

Point 4 needs an explicit exception or a different mechanism for recursive
definitions. A function body commonly needs to refer to the function being
defined, and a recursive type needs its type name available within its
definition. Definition-producing forms may therefore establish a name before
checking their bodies, while ordinary value initializers do not. Treating that
as a deliberate distinction is preferable to making all partially initialized
values self-visible.

#### Open design questions

##### Declaration and initialization

- Is `:=` lexically one operator or the formatted adjacency of `:` and `=`?
  Programmer-facing meaning can be settled before formal tokenization, but
  whitespace, formatting, and diagnostics eventually depend on the answer.
- Does every type have default initialization, or can `name : Type` fail to
  compile when the type has no default?
- Is default initialization allowed to perform visible work, allocate, or fail?
  If so, how does the syntax satisfy the vision's visible-cost principle?
- Is `???` the intended explicit uninitialized mechanism, and in which safety,
  storage, and lifetime contexts may it appear?
- Can inference determine both a type and qualifiers such as reference,
  mutability, ownership, or lifetime policy, or must some policies remain
  explicit?
- Are literal types inferred as one fixed default, contextually from later use,
  or only from the initializer itself?

##### Visibility, redeclaration, and shadowing

- Does an ordinary declared name enter scope after its entire initializer, after
  type resolution but before value evaluation, or at another precise point?
- If nested shadowing remains legal, should it require an explicit marker or
  produce a mandatory diagnostic unless acknowledged?
- How can a programmer intentionally assign to an outer binding after declaring
  an inner binding with the same name? Legacy namespace material offers
  `Module` for globals and aliases for types, but no general outer-scope
  qualifier.
- Are value names, type names, aliases, imports, labels, parameters, and members
  in one collision domain or several?
- Do declarations in separate operands of one composed statement share the same
  redeclaration rules as vertically separate statements in a block?

##### Assignment

- Is assignment an expression that produces a value, or only a statement-level
  operation? Calling it a binary overloadable operator in the legacy list is not
  enough to settle expression behavior.
- What part of `=` may user code overload: storage replacement, conversion,
  resource transfer, or only type-specific assignment after name and destination
  resolution?
- Are compound assignments and increment/decrement always mutations requiring an
  existing writable destination?
- How do `final`, `constant`, `immutable`, and later mutability rules distinguish
  "may be initialized once" from "may be reassigned"?
- Is assigning a function body after `functionName : FunctionType` ordinary
  assignment to a default-initialized function value, completion of a deferred
  declaration, or a special definition operation?

##### `::` and non-value declarations

- What single programmer-facing idea, if any, unifies named types, enums,
  aliases, named imports, and unnamed imports?
- Is `::` a declaration introducer, part of each following declaration form, or
  a namespace-level binding operator?
- Which `::` declarations establish their names before their bodies so recursive
  references work?
- Are `final ::` function examples meaningful, obsolete, or typographical?
- Should build-time value bindings continue to use the ordinary `:`/`:=` forms
  with execution context expressed separately?

##### Declaration positions and contextual inference

- Which positions accept a general declaration, which accept only a restricted
  binding, and which accept an ordinary declaration statement?
- Do flow-control initializers accept any simple declaration statement or a
  narrower form?
- Can `name:` appear without a following initializer when another construct
  supplies the type and value context? If so, exactly which constructs provide
  that context?
- In a multi-result declaration, is one `=` shared by several independently
  declared names, and is initialization atomic if conversion or construction of
  a later result fails?
- Do defaulted parameters use declaration initialization syntax or a separate
  default-argument relationship that merely shares `=` punctuation?
- For stored members, is `=` evaluated once per instance, once per type, or
  according to a separate storage qualifier? Declaration syntax should not hide
  that distinction.

#### Corner cases to test against any proposal

```zax
x = 1
```

- If no `x` is visible, this must diagnose an unresolved assignment target
  rather than declare `x`.

```zax
x := x + 1
```

- With no outer `x`, the initializer should not read an uninitialized new `x`.
- With an outer `x`, the syntax tests whether shadowing is legal and whether the
  right-hand `x` resolves outward.

```zax
x : Integer
x := 1
```

- The second line tests same-scope redeclaration. It should not be interpreted as
  initialization of the first declaration merely because that declaration has
  not otherwise been used.

```zax
x : Integer; x = 1
```

- This tests default initialization followed by assignment in one composed
  statement. If the intent is initialize-later rather than default-then-assign,
  the language needs a distinct model and definite-initialization rules.

```zax
if result := tryValue() ;; result.isValid()
    use(result)
```

- This tests when the header binding enters scope, whether it is visible to the
  condition and every clause body, and where it ceases to exist.

```zax
first:, second: = produceTwo()
```

- This tests contextual inference, duplicate names, discarded results,
  per-result conversion, and partial initialization failure without requiring
  this work item to design destructuring generally.

```zax
factorial : (result : Integer)(n : Integer) = {
    return n <= 1 ?? 1 ;; n * factorial(n - 1)
}
```

- This tests recursive function-name visibility without exposing an ordinary
  partially initialized value.

```zax
Node :: type {
    next : Node *
}
```

- This tests recursive type-name visibility and whether definition-producing
  `::` forms need different scope-entry timing from value declarations.

```zax
/// Describes x.
x := makeValue()
```

- Leading documentation should attach to the declaration. For a declaration
  used as a flow initializer or as an operand of a composed statement, the
  attachment target must remain structurally unambiguous under the accepted
  source model.

#### Immediate diagnostic and formatting implications

Without choosing exact diagnostic identifiers, the model needs diagnostics that
distinguish:

- assignment to an unresolved name from declaration;
- same-scope redeclaration from permitted or prohibited nested shadowing;
- use of a name before its initialization completes;
- a declaration lacking enough information to determine a type;
- a type mismatch or failed construction in an initializer;
- assignment to a non-writable binding;
- a declaration form used in a position that accepts only an expression or only
  a restricted binding; and
- an unsupported or ambiguous `::` declaration form.

Formatting should make the declaration boundary obvious. If the compact inferred
form remains `name := value`, the formatter should use that spelling
consistently while diagnostics still explain declaration and initialization as
separate concepts. Typed declarations should preserve the readable parallel:

```zax
inferred := value
explicit : Type = value
```

This visual relationship is a concrete benefit of the punctuation, but it only
earns its unfamiliarity if bare assignment remains unambiguously non-declarative
and the exceptional contextual forms do not undermine that rule.

#### Consequences recorded without premature resolution

- Function parameters, results, captures, default arguments, and recursion must
  eventually conform to the common binding model, but complete function
  semantics remain a later concern.
- Flow-control headers need a restricted declaration position whose scope agrees
  with the accepted clause-scope model. The legacy `;;` separator remains
  unaccepted and is not decided here.
- Default initialization, explicit uninitialized storage, constructor
  selection, initialization failure, destruction, and partial initialization
  require later lifetime and construction work. This review must avoid a
  declaration model that makes those questions impossible to answer.
- Structural typing may depend on whether member declaration shape includes
  names, qualifiers, defaults, or inferred types. No structural-equivalence rule
  is needed to establish the basic declaration distinction.
- The exact taxonomy of `::` may deserve a focused continuation if type, alias,
  import, and build-time declarations cannot be made coherent without expanding
  this work item beyond ordinary value bindings.

## Dispositions and promotion dry run

### Readiness conclusion

The aligned findings are ready for a separately discussed and authorized
promotion. No unresolved non-deferred contradiction remains. The deferred
questions constrain later work without preventing a coherent programmer-facing
declaration model now.

This dry run proposes documentation ownership and an exact promotion change set.
It does not promote the findings, accept a formal grammar, authorize the proposed
edits, close work item `003`, archive this file, or begin work item `004`.

### Proposed lasting structure

Retain the current flat `language/` layout and add one concept owner:

```text
language/
    vision.md
    source-structure.md
    declarations-and-bindings.md
```

The proposed `language/declarations-and-bindings.md` is cohesive despite the
number of interactions it records. Name introduction, initialization,
visibility, shadowing, place qualification, assignment boundaries, and
non-value definition forms all contribute to one programmer question: what a
declaration introduces and how that binding may subsequently be used.

Splitting qualifiers, names, scope, or non-value definitions into separate
owners now would duplicate the common binding model and create navigation
without an independently mature concept. A later documentation fit review may
split the owner if those concerns develop distinct audiences or lifecycles.

No new directory, `language/README.md`, formal specification area, grammar file,
implementation document, compatibility stub, or raw placeholder is needed.

### Lasting owner boundaries

The new declarations owner should use the standard metadata fields and state:

| Field | Proposed content |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing declaration, binding, initialization, name-resolution, and assignment boundaries; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Value declaration forms; default, direct, inferred, and explicitly bypassed initialization; binding visibility; redeclaration and shadow permission; one lexical identifier namespace; qualified-path resolution through incomplete declarations; explicit instance-member lookup; binding, value, and access qualifier axes; the declaration-versus-assignment boundary; the general non-value definition family; `forward` and `Self` at the depth required by declarations; declaration diagnostics and formatting |
| Does Not Own | Complete inference, function and capture semantics, operator resolution, constructors and lifetimes, move/copy and ownership, detailed mutability, flow-control grammar, import/module behavior, type identity and layout, formal grammar, diagnostics identifiers, or implementation |

The owner should be rewritten for human developers rather than copied from the
chronological working record. A suitable reading order is:

1. Mental model and core forms.
2. Default, direct, inferred, and `???` initialization.
3. Scope entry, redeclaration, and `shadowable`.
4. Identifier namespace, explicit `_.` member access, and qualified paths.
5. `final`/`varying`, `mutable`/`immutable`, and
   `writable`/`readonly`.
6. Assignment and overload-selection boundaries.
7. Non-value definitions, `forward`, and `Self`.
8. Declaration contexts, documentation, diagnostics, and formatting.
9. Explicit boundaries and deferred interactions.

The accepted [language vision](../../../language/vision.md) and
[source-structure owner](../../../language/source-structure.md) are prerequisites.
The new owner should link back to them without duplicating their principles or
source-formation rules.

### Ownership map

| Aligned material | Lasting owner and disposition |
| --- | --- |
| `name : Type`, `name : Type = value`, `name := value`, and assignment that never declares | New `language/declarations-and-bindings.md`; central accepted model |
| Direct initialization rather than default-initialize-then-assign | New declarations owner; construction mechanics remain deferred |
| Every ordinary declaration initializes; `???` skips construction but retains ordinary destruction | New declarations owner; complete lifetime, unsafe-boundary, optimization, and analysis rules remain deferred |
| Initializer-only inference and partial type-shape qualification | New declarations owner as a programmer constraint; complete inference remains deferred |
| Visibility after initialization, same-scope redeclaration, and semicolon-composition consequences | New declarations owner, linked to source structure for effective statements and clause scope |
| Recursive function bindings as delayed-body references | New declarations owner as a constraint; representation and `forward` requirements remain future function work |
| `shadowable`, alias-before-shadow behavior, and no parent-scope lookup operator | New declarations owner |
| Mandatory `_.` access for instance members | New declarations owner; detailed member and function behavior remains future type/function work |
| One lexical identifier namespace, mandatory casing intent, aliases, stable roots, and non-backtracking paths | New declarations owner |
| Pending suffix resolution through an already selected incomplete declaration | New declarations owner; detailed dependency algorithms remain implementation |
| Keyword-selection rationale used for `shadowable` | New declarations owner as local rationale; broader lexical policy may later move to a dedicated owner |
| `final`/`varying`, `mutable`/`immutable`, and `writable`/`readonly`; layered place qualifications; compatible and conflicting qualifiers | New declarations owner; complete qualifier grammar and cast lattice remain deferred |
| Move and copy are operations rather than persistent value qualifiers | New declarations owner as a boundary constraint; move/copy design remains deferred |
| Future capabilities may become qualifiers, concepts, operations, reflected properties, contracts, or combinations | New declarations owner as an explicit non-decision |
| Assignment and other overloadable operators are selectable functions, while declaration itself is reserved | New declarations owner at the declaration boundary; complete operator behavior remains future operator work |
| `::` as the non-value definition family, separate from build-time evaluation | New declarations owner at the shared binding level; individual type, enum, alias, import, and namespace semantics remain with future owners |
| `final ::` function forms are errors | New declarations owner and focused legacy corrections |
| Explicit named `forward`, `Self` for innermost or anonymous types, and no automatic incomplete self-name baseline | New declarations owner; complete forward and recursive-type behavior remains deferred |
| Declarations in flow initializers and nested block scope | New declarations owner for binding behavior; exact header grammar, `;;`, and layout remain future flow/source work |
| Stored-member initialization per storage instance and the `once` boundary | New declarations owner as a constraint; complete storage and construction semantics remain deferred |
| Multi-result declarations share an initializer and documentation targets the complete declaration | Declaration behavior belongs to the new owner; documentation attachment remains owned by `language/source-structure.md` |
| Declaration diagnostics and canonical formatting | New declarations owner at the programmer-visible level |
| Intent-sensitive correction of legacy examples | `project/documentation.md`; reusable documentation practice rather than language semantics |
| Member declaration names, qualifiers, defaults, inference, and recursive forms may affect structural identity | Existing `project/raw/structural-typing.md`; preserve as a future question without deciding equivalence |

### Existing owner and guidance updates

#### `language/source-structure.md`

Keep its current ownership. Update its `Does Not Own` and boundary wording to link
declaration behavior to the new owner rather than describing all declaration
syntax as later work. Add the smallest clarification that documentation on a
single or multi-binding declaration attaches to the complete declaration
construct. Do not move statement, body, semicolon, layout, or comment ownership.

#### `language/vision.md`

Add the declarations owner to `Current maturity` beside source structure. No
design principle needs rewriting; the new model already follows the accepted
readability, explicit-policy, visible-cost, and progressive-complexity
principles.

#### `project/documentation.md`

Add compact reusable guidance for legacy example correction:

- snippets may intentionally omit surrounding declarations;
- determine intended semantics from prose and context;
- preserve assignment examples that assume existing names;
- correct declaration punctuation when doing so clarifies existing intent;
- add only the smallest useful context;
- record ambiguity instead of deciding new language behavior; and
- avoid unrelated corpus-wide cleanup during focused concept promotion.

This is process guidance, not language design.

#### `project/raw/structural-typing.md`

Add a non-authoritative future constraint from work item `003`: a structural
typing review must decide whether member names, `final`/`varying`,
`mutable`/`immutable`, `writable`/`readonly`, defaults, inferred types,
`forward`, and recursive `Self` forms participate in identity, equivalence,
layout, conversion, and reflection. Do not answer those questions or rewrite the
preserved candidate proposal.

The raw input is already indexed, so `project/raw/README.md` needs no change.

### Navigation and reading path

Update `index.md` in two places:

- add the declarations owner to `Start here`, describing declaration,
  initialization, binding visibility, qualifiers, and assignment boundaries;
- add it to `Current conceptual design`.

`README.md` already routes public readers through `index.md` and needs no change.
`project/README.md` remains the current-work index and needs no promotion edit.
Its active pointer changes only during separately authorized closure and
continuation.

The resulting ordinary reading path is:

1. `language/vision.md` for foundational direction;
2. `language/source-structure.md` for source formation;
3. `language/declarations-and-bindings.md` for name introduction and use;
4. a focused legacy page only for an unreviewed adjacent concern.

No current owner should link to this work item or its future archived path for
meaning.

Cross-links in the promoted documentation should identify a concrete ownership
boundary, migrated concern, or conceptual dependency. Generic sibling discovery
belongs in `README.md` and `index.md`. In particular, the declarations owner does
not need a generic footer linking to the vision; its source-structure links
remain because scope composition and documentation attachment cross those two
owners directly.

### Legacy-page supersession notices

The root index already labels every root topic page as legacy. Add a short,
topic-specific notice near the top of each page below so readers encountering a
deep link reach the accepted declarations owner before contradictory legacy
claims:

| Existing file | Focused notice or correction |
| --- | --- |
| `basics.md` | Extend the current source-structure notice to link the declarations owner; remove declarations from the list described as wholly unreviewed |
| `type-definition.md` | Link accepted value/member declaration, default initialization, `???`, `forward`, and `Self` boundaries; retain detailed type construction as legacy |
| `functions.md` | Link accepted function-value declaration, recursive-binding, qualifier, and assignment boundaries; retain complete function behavior as legacy |
| `flow-control.md` | Link accepted declaration visibility and initializer-scope behavior; retain exact control forms and `;;` as legacy |
| `scope.md` | Link accepted binding visibility, same-scope redeclaration, shadow permission, and qualified lookup; retain lifetime and control-transfer material as legacy |
| `namespacing.md` | Link the accepted one-identifier-namespace, `shadowable`, explicit member access, and path-resolution model; retain module/import details as legacy |
| `forward.md` | Link accepted forward-completion and pending-path constraints; retain the complete forward catalog as legacy |
| `mutable.md` | Use the accepted `varying`, `writable`, and `readonly` terminology throughout qualifier material; retain unreviewed detailed mutability proposals as evidence |
| `ctor-dtor.md` | Link accepted declaration-level default/direct/`???` initialization and guaranteed-destruction boundaries; retain detailed constructor/destructor mechanics as legacy |
| `nothing.md` | Link the accepted default function-binding boundary and state that complete `Nothing` behavior remains unreviewed |
| `operator.md` | Link the accepted declaration-versus-assignment and qualified-candidate-selection boundary; retain complete operator declaration and resolution behavior as legacy |

Do not add notices to every page containing a declaration example. The new
owner, current-design navigation, root legacy notice, and the focused pages above
are enough to prevent competing authority.

### Focused legacy corrections during promotion

Correct only examples whose intent is unambiguous and directly contradicts the
aligned declaration model:

- In `functions.md`, replace both `saveToDisk final ::` spellings with
  `saveToDisk final :`.
- In `namespacing.md`, replace `addOne final ::` with `addOne final :`; because
  its body is visibly accessing a member, use `++_.value3`.
- In `flow-control.md`, replace the nine bare `while i:` initializer examples in
  the focused loop section with `while i : Integer`, preserving their intended
  default initialization.
- In `operator.md`, correct the introductory equality example from `=` to `==`
  so it does not call assignment an equality operator.

After review of the first promotion result, also:

- replace `varies` with `varying` where it is the place qualifier;
- replace `constant` with `readonly` where it is the access qualifier;
- replace `inconstant` with `writable` where it is the access qualifier;
- preserve ordinary English, compile-time constants, `is constant`, and
  diagnostic identifiers such as `constant-overflow` and `constant-syntax`;
- update the intentional namespacing shadow example so its outer type is
  `shadowable`;
- replace the legacy dotted-name backtracking description with fixed-root,
  pending-suffix resolution; and
- update the shadowing diagnostic to apply when the hidden declaration did not
  grant permission.

The qualifier migration requires semantic classification rather than blind
replacement. Ambiguous occurrences must be resolved from their local intent or
left with an explicit concern.

The focused audit resolved three initially ambiguous cases:

- the basics disambiguation example follows the renamed `readonly` keyword;
- `debug final : readonly = ...` in the namespacing module options uses the same
  inferred-base-type access-qualifier form as other legacy examples; and
- compile-directive inputs require `readonly` or `immutable` access in addition
  to being evaluated at compile time.

Other surviving uses of `constant` describe compile-time values, compound
operations such as `is constant`, diagnostic identifiers, or ordinary English.

Do not sweep other examples for implicit member access, capitalization, `Self`,
or declaration completeness. Those pages remain legacy, and many correct forms
depend on their own future concept reviews. Apply the intent-sensitive correction
rule rather than making every fragment independently compilable.

### Promotion corrections after review

Review of the first promoted owner identified these required refinements:

- explain the direct `final`/`varying` model before advanced aliasing;
- demonstrate references with an actual source value;
- establish only that references and captures preserve or reduce a source
  place's capability;
- defer independent reference-binding and referent-replacement syntax;
- describe move and copy positively as contextual operations without naming
  nonexistent qualifier tokens;
- remove the historical `final ::` typo and dedicated diagnostic from the
  lasting owner; and
- remove a generic vision footer while retaining concern-specific
  source-structure links.

### Superseded and retained legacy material

Promotion should explicitly supersede these declaration-level legacy ideas:

- assignment may create an unresolved destination;
- bare `name:` can infer a type without contextual initialization;
- ordinary functions may use `final ::`;
- `varies` is the positive spelling paired with `final`;
- `constant`/`inconstant` is the accepted access-path pair;
- ordinary nested shadowing needs no permission;
- instance members participate in unqualified local lookup;
- separate type and value identifier namespaces are available by default;
- qualified lookup may backtrack to another root when a chosen path fails; and
- a type's unforwarded own name is automatically incomplete inside its body.

Retain the underlying legacy pages as evidence for unreviewed details. Their
notices and the root index make their maturity explicit; no relocation or
compatibility stub is needed.

### Explicitly deferred destinations

The new declarations owner should retain concise boundary constraints so future
work can discover them without consulting an archive:

- functions and captures: recursive binding representation, mutual recursion,
  parameter defaults, result mapping, function `Nothing`, and exact capture
  timing;
- types and inference: complete inference, anonymous and recursive type
  mechanics, structural identity, and qualifier participation;
- initialization and lifetime: low-level initialization gates, `???` analysis,
  construction, destruction, panic cleanup, relocation, RVO, and NRVO;
- qualifiers, move, and copy: exact grammar, cast lattice, reference and capture
  projection, transfer selection, ownership, and source state;
- operators: built-in result behavior, value categories, overload ranking,
  conversion, and generated candidates;
- flow control: exact header grammar, `;;`, and mandatory layout;
- modules and names: import behavior, overload-group and label collision rules,
  and detailed forward declarations;
- broader capabilities: later reviews decide whether they become qualifiers,
  concepts, operations, reflected properties, contracts, or combinations; and
- formalization and implementation: grammar, diagnostic identifiers, compiler
  data structures, lowering, and conformance remain future owners.

The structural-identity consequence also belongs in the existing raw input as
described above. No additional raw file is needed.

### Exact proposed promotion change set

The coherent promotion consists of these files and no others:

1. Add `language/declarations-and-bindings.md`.
2. Update `index.md`.
3. Update `language/vision.md`.
4. Update `language/source-structure.md`.
5. Update `project/documentation.md`.
6. Update `project/raw/structural-typing.md`.
7. Update focused legacy notices in:
   - `basics.md`;
   - `type-definition.md`;
   - `functions.md`;
   - `flow-control.md`;
   - `scope.md`;
   - `namespacing.md`;
   - `forward.md`;
   - `mutable.md`;
   - `ctor-dtor.md`;
   - `nothing.md`; and
   - `operator.md`.
8. Apply only the four focused legacy correction groups listed above.
9. Apply the reviewed qualifier migration and related corrections in:
   - `alias.md`;
   - `arrays.md`;
   - `basics.md`;
   - `casting.md`;
   - `compiler-directives.md`;
   - `concurrency.md`;
   - `ctor-dtor.md`;
   - `except.md`;
   - `flow-control.md`;
   - `functions.md`;
   - `mutable.md`;
   - `namespacing.md`;
   - `operator.md`;
   - `pointers.md`; and
   - `warnings-errors.md`.
10. Refine `language/declarations-and-bindings.md` and
    `project/documentation.md` as described above.
11. Update this working record to preserve the reviewed promotion corrections
    and final validation scope.

Promotion must be separately discussed and authorized. Closure, archival,
creation of work item `004`, and updating the current-work index are later
separate actions after promotion validation.

### Proposed promotion validation

Validate that:

- every accepted declaration concern has exactly one owner;
- the new owner states conceptual, not specification or implementation,
  authority;
- source-structure and declarations ownership do not overlap;
- current navigation reaches the new owner;
- every legacy page receiving a supersession notice identifies its remaining
  content as legacy;
- `final ::` function examples and bare `while i:` forms are absent from the
  focused corrected locations;
- `varying`, `writable`, and `readonly` are used consistently in current
  conceptual documentation;
- `varies` and `inconstant` remain only in explicitly historical working
  material;
- `constant` remains in ordinary legacy reading only for compile-time,
  compound-operator, diagnostic-identifier, or ordinary-English meanings;
- reference examples do not claim unsettled rebinding behavior or permit a final
  referent to regain varying capability;
- current conceptual documentation does not present nonexistent move/copy
  qualifier spellings;
- assignment examples retain their existing intent;
- deferred material remains discoverable without relying on archived work;
- raw structural-typing input remains clearly non-authoritative;
- local links and heading anchors resolve;
- Markdown renders coherently;
- no formal grammar, implementation mapping, or conformance claim appears;
- staged and unstaged review boundaries remain as established by the language
  maintainer; and
- the final changed-file set matches the separately authorized promotion scope.

### Promotion and closure result

On 2026-08-21, the language maintainer authorized the dry-run promotion and its
reviewed terminology, reference-capability, cross-link, lookup, and shadowing
corrections. Promotion:

- created [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  as the current conceptual owner;
- updated current navigation, vision maturity, and source-structure ownership
  boundaries;
- established focused legacy supersession notices and reusable example-migration
  and cross-link guidance;
- migrated settled qualifier terminology while preserving compile-time and
  ordinary-English uses of `constant`;
- reconciled legacy shadowing and path lookup with `shadowable`, fixed roots, and
  pending suffix resolution;
- preserved structural-typing consequences as non-authoritative future input;
  and
- validated the authorized file set, links, Markdown, terminology, authority,
  owner independence, and maintainer-controlled staging boundary.

Every material finding was promoted, retained as an explicit boundary, recorded
as a future constraint, or marked as superseded history. Current documentation
does not depend on this archived record for meaning.
