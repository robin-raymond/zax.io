# 006: Function invocation, argument binding, and results

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `006` |
| Created | 2026-08-23 |
| Completed | 2026-08-24 |
| Owns | Historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current language behavior, current project guidance, accepted complete function, ownership, lifetime, generic, async, formal grammar, or compiler behavior |

## Non-authority notice

This file is a historical, audit-only working record. Its aligned findings were
promoted into current owners before archival. It remains non-authoritative and
must not be used as an ordinary source of current language behavior.

## Fixed initiating input

This section records the information known and aligned when work item `006` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for ordinary function
invocation:

1. how arguments map to parameters;
2. when expressions evaluate and parameters bind;
3. how defaults and omitted arguments work;
4. how zero, one, or multiple results map into surrounding expressions and
   declarations;
5. how overload candidates become viable, preferred, ambiguous, or unavailable;
   and
6. how temporaries and immediate reference, copy, move, or `last` effects cross
   the call boundary.

Use construction as precedent where its argument behavior is general without
forcing ordinary calls to use construction-packet or stored-member syntax.

### Motivating pressure

Work item `005` established strict left-to-right construction-packet evaluation,
immediate binding according to parameter semantics, named and positional cursor
behavior, declaration expressions as inputs, temporary-lifetime requirements,
resultful and resultless replacement, and demand-driven operation selection.

Those findings deliberately defer their general callable form. Existing
declaration and qualifier owners also establish function-value declarations,
parameter/result declaration contexts, recursive binding boundaries, qualified
arguments, receiver operands, and operator selection without yet owning complete
ordinary invocation behavior.

Reviewing function invocation next tests whether those boundaries form one
predictable call model before later pointer/lifetime, unsafe-analysis, async,
generic, variadic, or complete function-value work depends on it.

### Known assumptions

- The current [language vision](../../../language/vision.md) is accepted
  foundational direction.
- [Zax source structure](../../../language/source-structure.md) is accepted
  conceptual design.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  is accepted conceptual design.
- [Zax qualifiers](../../../language/qualifiers.md) is accepted conceptual design.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
  is accepted conceptual design.
- Parameters and results are declaration contexts using ordinary name, type,
  initialization, and qualifier concepts.
- Function values use ordinary value declarations; an ordinary function body is
  delayed code rather than executable declaration initialization.
- Instance members are accessed explicitly through `_`; member-function
  invocation and overload selection use a receiver operand.
- Construction establishes strict left-to-right argument evaluation and
  immediate copy, move, `last`, reference, pointer, or other input binding.
  Ordinary invocation should preserve that behavior unless a concrete
  contradiction requires explicit revision.
- Construction's stored-member packet entries are construction-specific and do
  not become ordinary function arguments.
- Generated and declared callable behavior must not be selected through
  source-order guessing when no candidate clearly wins.
- Exact move/copy, `last`, pointer lifetime, ownership, generic, variadic,
  capture, async, and concurrency behavior remains later work unless an
  immediate invocation contradiction requires a constraint.
- There is no language implementation in this repository.

### Known inclusions

- Ordinary function-call syntax and expression boundaries.
- Zero, one, and multiple input arguments.
- Positional and named argument syntax.
- The positional cursor after named arguments.
- Parameter names as source-facing call-contract elements.
- Declarations used as argument expressions.
- Strict argument evaluation and immediate parameter binding.
- References, pointers, copy, move, and `last` at the immediate call boundary.
- Parameter initialization and complete call-entry state.
- Default parameter expressions and omitted arguments.
- Zero, one, and multiple results.
- Result capture, mapping, omission, and discard.
- Resultless calls used as statements or nested expressions.
- Return completion at the depth needed to make result mapping coherent.
- Function overload viability, preference, ambiguity, and unavailability.
- Expected-result context in candidate selection.
- Calling function values versus resolving overload groups.
- Member-function calls and immediate receiver-operand qualification.
- Temporary lifetime through call completion.
- Later overload changes and source-stability pressure.
- Immediate callback and reentrancy consequences when needed to define call
  completion.
- Programmer-visible costs, diagnostics, and canonical formatting.
- Candidate lasting owners and documentation boundaries.
- Preservation of useful but premature pointer, ownership, generic, variadic,
  async, analysis, or safety proposals as indexed raw input.

### Known starting boundaries

- Complete closure and capture representation.
- Function reassignment and recursive function values beyond immediate
  invocation pressure.
- Complete move/copy/`last` ownership and moved-from states.
- Complete pointer/reference lifetime guarantees and provenance.
- Generics, concepts, and specialization.
- Variadic functions and argument splitting or combining.
- Async calls, coroutines, cancellation, executors, and scheduling.
- Arbitrary operator declaration and ranking beyond shared callable pressure.
- Complete callback-effect and reentrancy analysis.
- Foreign calling conventions and ABI.
- Formal grammar, compiler data structures, lowering, or implementation.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Exact ordinary-call syntax for positional and named arguments.
- Whether ordinary calls use the construction positional-cursor model without
  change.
- Whether positional arguments may follow named arguments in every callable
  category.
- How declarations nested inside arguments are formatted and scoped.
- When parameter default expressions evaluate relative to explicit arguments.
- Whether omitted parameters use the same marker or mechanism as explicitly
  requested type-default initialization.
- Whether parameter names are always part of the public source contract.
- How expected-result context affects overload viability and ranking.
- How zero, one, and multiple results participate in expression typing.
- How result capture, omission, discard, and declaration interact.
- Whether overload groups and function-valued expressions use one invocation
  surface.
- Exact temporary destruction points after copy, move, reference, or `last`
  binding.
- How a later overload or parameter-name change affects source compatibility.
- Which findings belong in current function documentation versus future
  lifetime, ownership, generic, variadic, operator, async, safety, or diagnostic
  owners.

### Initial stopping guidance

Stop when the work has:

- established a coherent ordinary invocation syntax and programmer model;
- established positional, named, defaulted, and omitted argument mapping;
- established strict evaluation and immediate binding behavior;
- established coherent zero, one, and multiple result production and capture;
- established immediate overload viability, preference, ambiguity, and
  unavailable-candidate behavior;
- constrained copy, move, `last`, references, pointers, temporaries, receiver
  operands, callbacks, and result context without designing their complete
  future domains;
- identified programmer-visible costs, diagnostics, and formatting;
- dispositioned the indexed function-invocation input;
- preserved useful deferred function, lifetime, analysis, generic, variadic, or
  async material in appropriate indexed input;
- identified plausible lasting owners and their boundaries; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `007` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides explicit-cost,
  progressive-complexity, readability, and source-stability principles.
- [Zax source structure](../../../language/source-structure.md) - provides
  expression, statement, body, semicolon-composition, and scope boundaries.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides function-value declarations, parameters/results as declaration
  contexts, recursive bindings, name visibility, and result-capture boundaries.
- [Zax qualifiers](../../../language/qualifiers.md) - provides parameter, result,
  capture, receiver-operand, and access qualification.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md) -
  provides the accepted evaluation, binding, cursor, temporary, generated
  operation, and result precedents that ordinary invocation must test.
- [Zax language-design terms](../../../language/terms.md) - provides current
  cross-cutting vocabulary.
- `project/raw/function-invocation.md` - preserved the construction-derived
  constraints and examples consumed by this work; retired during authorized
  promotion after every retained finding received a destination.
- [Legacy functions](../../../functions.md) - preserves primary evidence for
  parameters, results, calls, overloads, defaults, member functions, transfers,
  and argument-composition proposals.

### Consequence-driven

- Read focused [operator](../../../operator.md) material when callable ranking,
  expected-result selection, or operator/function consistency becomes necessary.
- Read focused [pointer and reference](../../../pointers.md) material and the
  [raw lifetime-strategies input](../../raw/lifetimes.md) when reference origin,
  temporary extension, move/copy/`last`, or ownership becomes necessary.
- Read the [raw analysis-controls input](../../raw/analysis-controls.md) when
  invocation requires compiler proof, unsafe overrides, or diagnostic
  provenance.
- Read the [raw safety input](../../raw/safety.md) when invocation behavior crosses
  a promised safe-subset boundary.
- Read focused [variadic](../../../variadic.md) material when fixed-arity invocation
  cannot be established without a variadic constraint.
- Read focused [flow-control](../../../flow-control.md) or [Nothing](../../../nothing.md)
  material when result mapping or resultless calls cross those boundaries.
- Read the [raw async input](../../raw/async.md) when call completion, suspension,
  cancellation, or callback behavior becomes materially async.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `005`.

Do not read work item `005` while conducting ordinary work on `006`. Its
applicable findings have been promoted into
`language/construction-and-destruction.md` and preserved in
`project/raw/function-invocation.md`; reading the archived work would add
historical discussion rather than current authority.

Read work item `005` only when the language maintainer explicitly requests a
targeted audit or provenance investigation. Other audit-only material likewise
requires direct language-maintainer instruction or a specific audit or recovery
need.

## Working record

Unless a subsection explicitly records later alignment, this working record is
raw reconstruction or candidate analysis for review. Aligned findings remain
non-authoritative until separately promoted into their lasting owners.

### Recovered intent and constraints

The strongest common thread in the current owners and legacy material is that a
call should be understandable as a boundary with three distinct phases:

1. determine one callable and map source arguments to its parameter slots;
2. evaluate each supplied input and establish every parameter; and
3. execute the body and map its declared results into the surrounding context.

That separation matters because source mapping, runtime order, and overload
selection answer different questions. A named argument may map to a different
parameter for each overload candidate, but selecting the candidate must not
change the source order in which argument expressions run. A reference and a
copy may bind the same source expression at the same point while observing
different later behavior. A typed result destination may constrain which
callable is usable without becoming permission to inspect arbitrary later uses
of an inferred declaration.

The evidence supports these constraints:

- Ordinary invocation already uses the familiar `callee(...)` surface throughout
  the legacy corpus.
- Function values are ordinary values. Calling a resolved function value and
  invoking a selected declaration can share a call surface without pretending
  that dynamic function-value invocation performs overload lookup.
- A type-defined member call has a receiver operand. The receiver is not an
  invisible lexical member namespace; `_` remains the explicit current-instance
  access inside the body.
- Parameter and result prototypes are declaration contexts. Their names, types,
  and qualifications have programmer-visible meaning even though they are not
  ordinary declaration statements.
- Construction establishes the strongest current precedent for calls: strict
  left-to-right source evaluation, immediate input binding, candidate-specific
  named/positional mapping, complete entry state before the body, and temporary
  lifetime through completion of the selected operation.
- The construction packet's `.member = expression` entries are not general
  arguments. Their cursor-invalidating behavior therefore has no ordinary-call
  counterpart.
- Copy, move, `last`, reference, and pointer behavior is observable at the call
  boundary. Optimization may remove unobservable storage or transfers but may
  not silently choose different source semantics.
- Result arity is not merely an implementation detail. Zero, one, and multiple
  declared results need distinct consumption rules.
- Current construction and declaration material distinguishes a candidate that
  matches well from one that is actually invocable. A best-matching unavailable
  operation must not silently fall through to a weaker operation.
- Source-order selection is inconsistent with the current direction. Legacy
  operator material proposes import-order fallback, but the active assumptions
  and generated-operation design require unresolved equal matches to be
  diagnosed instead.

### Candidate ordinary-call surface

The candidate surface keeps ordinary positional arguments unmarked and uses a
parameter label followed by `:` for named arguments:

```zax
send(stream, bytes)
send(stream, encoding: utf8, bytes)
```

This deliberately differs from construction packets:

```zax
value : Message = [{
    : stream,
    encoding: utf8,
    .cache = cache
}]
```

The leading `:` remains useful in a construction packet because it distinguishes
a positional constructor argument from the packet's stored-member category.
Ordinary call parentheses have no stored-member category, so requiring
`send(: stream, : bytes)` would add punctuation without adding information.

The same call surface applies to:

```zax
declaredFunction(input)
functionValue(input)
instance.memberFunction(input)
makeInstance().memberFunction(input)
```

For a resolved function-valued expression, the expression's prototype supplies
the call contract. For an overload group, candidate selection resolves one
declaration before runtime argument evaluation begins. An overload group does
not automatically become a runtime function value; conversion or binding to a
particular function type would have to select a compatible member of the group.

The callee expression is evaluated once before its explicit arguments. In a
member call, the receiver expression is likewise evaluated once before the
explicit arguments and binds the receiver operand for the selected call:

```zax
makeTarget().apply(first(), second())
```

The candidate runtime order is `makeTarget()`, `first()`, `second()`, then the
body of `apply`. Static candidate analysis may inspect all relevant types and
qualifications, but it does not execute these runtime expressions.

### Candidate argument mapping

For each overload candidate independently:

1. Start a positional cursor at the first parameter.
2. A positional argument binds the parameter at the cursor and advances it.
3. A named argument binds the parameter with that source-facing name and resets
   the cursor to the following parameter.
4. A positional argument is not allowed to skip a parameter already at the
   cursor, search for another compatible slot, or wrap to an earlier slot.
5. Binding one parameter twice, naming an unknown parameter, or supplying a
   positional argument when no parameter remains makes that candidate nonviable.
6. After explicit mapping, every unbound parameter must have an applicable
   declared default or another explicitly accepted omission mechanism.

This preserves the construction cursor without importing its member-entry rule:

```zax
configure(host, retries: 3, true)
```

For a prototype such as:

```zax
configure final : ()(
    host : Host,
    retries : Integer,
    verbose : Boolean
) = {
}
```

`host` maps positionally, `retries: 3` maps by name, and `true` maps to the
parameter after `retries`, which is `verbose`.

Mapping remains candidate-specific:

```zax
process(value, mode: selectedMode, trailing)
```

Two candidates may put `mode` in different positions, so `trailing` may map to
different parameters. That is acceptable only if later viability and preference
produce one clear winner. An equal viable set is ambiguous rather than resolved
by declaration order.

#### Declaration expressions as arguments

The existing construction precedent permits an argument expression to declare a
temporary. The ordinary syntax can preserve the distinction between the outer
argument label and the inner declaration:

```zax
consume(
    temporary : String,
    options: (localOptions : Options)
)
```

The parentheses around the named argument's declaration are a readability
candidate, not yet a grammar decision. A formatter should add them when the two
uses of `:` would otherwise be difficult to parse.

The declaration-expression evidence does not yet settle the declared name's
scope. Plausible choices include the containing expression, the complete call,
or the surrounding statement. The call model only requires the produced value
and its lifetime to remain available through its parameter's use. Extending the
binding into later source would be a declaration-and-scope decision rather than
an incidental call-syntax consequence.

### Candidate evaluation and call-entry model

After one candidate is selected, explicit arguments evaluate strictly in source
order. Each argument immediately initializes or binds its already mapped
parameter before the next argument begins:

```zax
source : Integer = 1

observe(
    source,
    action: changeSourceToTwo(source)
)
```

If the first parameter copies, its parameter captures `1`. If it references
`source`, it binds while `source` is `1` but observes `2` when the body later
reads the referent. If it moves or consumes through `last`, that transfer also
occurs before `changeSourceToTwo` starts and may make the later argument invalid.

This is not an optimization preference. It is the programmer-visible order.
The compiler may elide a temporary, copy, or move only when doing so preserves:

- argument and callee side effects;
- the selected overload;
- the value or referent observed by each parameter;
- source availability after copy, move, or `last`;
- construction and destruction effects; and
- result production and capture.

Every parameter must be completely established before the body begins. A
callback invoked while evaluating an argument completes before the next argument
starts. A callback invoked by immediate parameter construction or binding is
part of that binding step. Reentrancy may observe already completed earlier
effects, but it must not observe a fiction that later parameters are already
bound.

### Candidate defaults and omission

Legacy material contains two useful but conflated ideas:

- omitting an argument so that a declared parameter default expression supplies
  it; and
- explicitly requesting the parameter type's ordinary default initialization
  instead of a declared default expression.

These should remain different operations.

A candidate declaration surface retains the familiar initializer form:

```zax
connect final : ()(
    host : Host,
    attempts : Integer = 3,
    trace : Boolean = false
) = {
}
```

Omission uses the declared parameter default:

```zax
connect(host)
connect(host, trace: true)
```

The legacy `:` placeholder remains evidence for explicitly requesting type
default initialization:

```zax
connect(host, :, true)
connect(host, attempts: :)
```

Whether that exact spelling should survive requires review. It must not be
treated as ordinary omission: `attempts` would receive `Integer`'s type default,
not its declared default expression `3`.

The legacy `#` placeholder is not necessary merely to reach a later positional
argument. Named arguments already express that intent:

```zax
connect(host, trace: true)
```

Using one marker both for "use the declared parameter default" and for result
discard also risks making `#` context-dependent in ways that obscure source.
The candidate therefore treats absence as the request for a declared parameter
default and leaves `#` for explicit result disposition unless later evidence
shows a separate input placeholder is worth its complexity.

#### Default evaluation order

The required evidence identifies default timing as unresolved. Two plausible
models have materially different observable behavior:

1. Fill a skipped default while walking parameter order, potentially inserting
   hidden effects between explicit source arguments.
2. Evaluate and bind all explicit source arguments first, then evaluate defaults
   for still-unbound parameters in parameter declaration order.

The second model is the current candidate because it preserves an uncomplicated
promise that visible argument expressions run left to right without hidden work
interleaved between them:

```zax
record(
    first: explicitFirst(),
    third: explicitThird()
)
```

If `second` has a default expression, the candidate order is
`explicitFirst()`, `explicitThird()`, then the default for `second`, then the
body. Default expressions would run once per actual call rather than when the
function is declared.

That model still permits surprising hidden cost after the final explicit
argument. Documentation and tooling should expose nontrivial defaults. The
alternative parameter-order model may be preferable if defaults are intended to
behave exactly like a sequence of parameter declarations. This is a decision
for review, not a recovered conclusion.

A conservative visibility rule would let a default expression refer only to
earlier parameter bindings and surrounding names, not the parameter being
initialized or later parameters. That follows ordinary declaration visibility,
prevents cycles, and remains deterministic even though later explicit arguments
have already been evaluated at runtime. Exact default-expression capture,
definition-site versus call-site lookup, and compile-time evaluation require
separate confirmation.

### Parameter names as call-contract elements

Named invocation makes parameter names source-facing. Renaming `attempts` in the
previous example would break callers using `attempts:` even if the function's
value-level type remained otherwise compatible.

The legacy corpus also says two function implementations can be compatible
despite different local parameter names. These ideas can coexist if the visible
prototype at the call site owns the labels:

```zax
callback : (result : Boolean)(input : Integer)
```

After assigning a compatible implementation whose local parameter is named
`value`, a call through `callback` would still use `input:` because
`callback`'s prototype is the source-facing contract:

```zax
okay := callback(input: 42)
```

Candidate implications:

- An overload declaration's parameter names are labels for calls resolved
  directly to that declaration.
- A function-valued expression's resolved prototype supplies its call labels.
- Assignment compatibility need not make local implementation names part of
  function-type identity.
- Two overloads must not differ only by parameter names. A positional call would
  be indistinguishable, and a later rename could silently change selection.
- A parameter intentionally unavailable to named calls needs an explicit
  anonymous/private-label form; the current evidence does not establish its
  spelling.

Parameter-label aliases or deprecation windows could reduce rename breakage, but
they are source-evolution tooling rather than a prerequisite for the basic call
model.

### Candidate result-shape model

A call has an ordered result shape determined by the selected prototype:

- `()` is zero results;
- `(value : T)` is one result; and
- `(first : A, second : B)` is two results.

Zero results are not a hidden `Nothing`, unit value, or implicit receiver result.
A resultless call is valid as an expression statement:

```zax
flush()
```

It cannot satisfy a value-required context:

```zax
value := flush()       // error: expected one result, found zero
consume(flush())       // error: this argument entry requires one value
1 + flush()            // error: the operator operand requires one value
```

Parentheses do not invent a value. A future sequencing or composition construct
may explicitly accept zero-result operands, but ordinary argument entries remain
one expression to one parameter slot. This keeps the construction-derived input
model intact and avoids making a resultless side effect silently disappear from
argument arity.

A one-result call participates in an ordinary one-value expression context:

```zax
doubled := double(21)
print(double(21))
```

A multiple-result call requires a context that explicitly consumes multiple
result slots:

```zax
left:, right: = divideWithRemainder(value, divisor)
#, remainder: = divideWithRemainder(value, divisor)
```

Here each `name:` is a declaration in a result-capture pattern and `#` consumes
one result without introducing a binding. The pattern maps positionally.
Result names may support future named mapping or combine operations, but those
legacy proposals should not silently affect the basic fixed-arity rule.

The candidate does not implicitly splice several results into several ordinary
arguments:

```zax
consumeTwo(produceTwo()) // unavailable without an explicit future splice form
```

That restriction keeps each argument expression responsible for one parameter
slot. Function composition, invocation chaining, variadic splitting, and
structural result combining can later provide explicit higher-level operations.

#### Required, discardable, and omitted results

Legacy functions distinguish results that must be captured from results marked
with `#` that callers may omit. That appears intended to prevent accidental loss
of important outcomes while allowing informational results to be ignored.

A coherent candidate interpretation is:

- every declared result must still be produced on every normal return;
- a result's declaration may state whether the caller may omit it;
- `#` at a capture site explicitly discards exactly one result;
- a shorter capture context may omit only results whose declarations permit
  omission; and
- using a resultful call as a bare statement is legal only when every result may
  be omitted, unless the source explicitly consumes them with a discard pattern.

For example, retaining the legacy marker provisionally:

```zax
measure final : (
    requiredValue : Integer,
    diagnostic # : String
)() = {
    // Both results must be completed.
}

value := measure()        // candidate: diagnostic may be omitted
value:, # = measure()     // explicit discard remains clearer
measure()                 // error: requiredValue was not consumed
```

The exact declaration spelling, whether explicit `#` should always be required,
and whether only trailing discardable results may disappear are open. Allowing
arbitrary implicit holes would make positional result mapping difficult to read.

Result discard is separate from a `Nothing` function value. Invoking a defaulted
function binding still diagnoses a provably unreplaced `Nothing` target or
panics when it is otherwise unhandled; it does not become a resultless no-op.

### Candidate return and completion model

Result declarations create ordered result obligations. On every normal return,
the body must have established a complete value for each declared result. A
zero-result function uses a bare `return` or reaches a permitted resultless
completion point. A resultful function cannot silently fall through and
manufacture type-default results.

For a direct return list:

```zax
return makeFirst(), makeSecond()
```

the candidate order mirrors argument binding:

1. evaluate `makeFirst()` and immediately establish the first result;
2. evaluate `makeSecond()` and immediately establish the second result; and
3. complete the call only after both result obligations are satisfied.

If establishing the first result transfers ownership or invokes observable
construction, those effects precede evaluation of the second expression.

The legacy corpus also treats result names as usable inside the body:

```zax
make final : (result : Item)() = {
    result.member = value
    return result
}
```

That raises an unresolved initialization question. If `result` is ordinarily
default-initialized on entry, the function may pay a constructor cost before
returning or replacing it. If it begins as result storage without a live value,
member access requires definite-construction rules. If `return result` moves
from a body-local value, its relationship to the declared result slot must be
defined. The current declaration principle that ordinary declarations always
initialize does not by itself settle this category-specific result context.

The candidate constraint is therefore narrower: result production must have
observable construction, copy, move, and destruction behavior, and every normal
completion must satisfy each result exactly once. The precise named-result
initialization model needs maintainer review before this part can become a
complete programmer model.

### Candidate selection model

Candidate selection should be explainable as viability followed by preference,
without runtime source-order guessing.

#### Candidate discovery

The callee form determines the initial set:

- an already resolved function-valued expression supplies one prototype;
- an ordinary identifier may identify an overload group;
- a member call identifies declarations reachable for the receiver operand; and
- an operator supplies the category-specific candidate sources owned by future
  operator design.

An already resolved function value does not dynamically search declarations
that happen to share its source name.

#### Viability

A candidate is viable only if:

- every explicit argument maps to one parameter;
- no parameter is mapped more than once;
- each omitted parameter has an applicable default;
- each argument can initialize or bind its mapped parameter with the required
  type, transfer mode, and qualifications;
- the receiver operand, when present, satisfies its declared qualification
  requirements;
- the candidate's result shape can satisfy an explicit surrounding result
  context; and
- the declaration is available for invocation.

Availability is checked separately from match quality. A bodyless requirement,
`forbidden` shape, unresolved generated-versus-existing demand, or default
`Nothing` function target may explain why the best-shaped operation cannot be
called. The exact declaration states applicable to ordinary functions remain
future function-declaration work, but weaker overloads should not become
surprising fallbacks merely because the better contract is unavailable.

#### Preference

The current evidence is not sufficient for a complete conversion lattice or
total ranking algorithm. A conservative candidate uses these principles:

1. Prefer exact argument and receiver matches over conversions or promise
   strengthening.
2. Treat required explicit copy, move, `last`, reference, pointer, and qualifier
   compatibility as part of match quality.
3. Use applicable defaults only after explicitly supplied matches; a candidate
   requiring fewer defaults may be a better match only if that preference is
   documented and cannot override a materially better explicit-input match.
4. Use an explicit expected-result context to remove candidates whose arity,
   type, or qualifications cannot satisfy it.
5. Do not inspect later statements to infer the desired result of `:=`.
6. Do not break an otherwise equal tie by declaration, import, generation, or
   source order.

Expected-result selection is useful and already has legacy pressure:

```zax
parse final : (result : Integer)(text : String) = {
}

parse final : (result : Float)(text : String) = {
}

integer : Integer = parse(source) // Integer result context
floating : Float = parse(source)  // Float result context
inferred := parse(source)         // ambiguous from this context alone
```

This candidate lets a typed destination make a candidate viable without letting
the later uses of `inferred` determine an earlier declaration. Whether result
quality should merely filter viability or also rank two convertible result types
remains open. Ranking on results is substantially more source-fragile than
checking exact result arity and compatibility.

An unresolved equal viable set is an ambiguity diagnostic. No candidate produces
an unavailable-call diagnostic. A uniquely best but unavailable candidate
produces an unavailable-operation diagnostic that identifies why weaker
candidates were not used.

### Temporaries, transfer, and call completion

The minimum synchronous call-completion boundary includes:

1. callee and receiver evaluation;
2. explicit argument evaluation and immediate binding;
3. omitted-parameter default evaluation and binding;
4. execution of the selected body;
5. production of all results; and
6. mapping or transfer of those results into the surrounding context.

A temporary bound to a reference parameter survives through that boundary:

```zax
inspect(makeBuffer())
```

If `inspect` receives `Buffer readonly &`, the temporary remains live while
`inspect` executes. That does not by itself permit a returned reference to
escape:

```zax
view := returnView(makeBuffer())
```

If `view` would refer into the argument temporary, the applicable lifetime
strategy must reject the escape, extend an owning lifetime through a defined
mechanism, or require an explicit unsafe operation. "Arguments survive the
call" is not "references returned from the call survive afterward."

For:

```zax
result := transform(makeInput())
```

the argument temporary survives until `transform` has completed and `result` has
been established according to the selected result transfer. Its later
destruction point must preserve all observable lifecycle effects. Exact
full-expression destruction order among several caller temporaries remains
lifetime work, but destroying a referenced argument before the body or before
result mapping is complete is incompatible with the call model.

The legacy proposals for automatically selecting `last` from whole-program
liveness are risky. They can change overload selection, source validity, and
resource transfer when an apparently unrelated later use is added or removed.
The candidate therefore requires explicit source intent whenever copy versus
move versus `last` changes programmer-visible semantics or candidate selection.
Static liveness may still justify optimization after the semantic operation is
known.

Immediate synchronous callbacks and reentrancy remain inside call completion.
Async suspension cannot reuse this boundary unchanged: argument temporaries,
captured references, cancellation, and result destinations may need to survive
well beyond the initiating expression. That is a concrete deferral to async and
lifetime work rather than permission to call suspension "ordinary completion."

### Diagnostics and visible costs

Useful invocation diagnostics should distinguish at least:

- no callable found for the callee form;
- an attempt to invoke a provably default-`Nothing` function value;
- an unknown named argument;
- duplicate binding of one parameter;
- a positional argument with no current parameter;
- a missing required parameter;
- a type, qualification, or transfer mismatch for a mapped parameter;
- an unavailable best candidate and the state making it unavailable;
- equal best viable candidates;
- a result-arity mismatch;
- an expected-result type or qualification mismatch;
- an unconsumed required result;
- a zero-result call used where a value is required;
- use of an argument source after an earlier move or `last` transfer;
- a reference result that would outlive its argument temporary; and
- receiver-operand qualification incompatibility.

When mapping differs between candidates, an ambiguity diagnostic should show the
candidate-specific parameter mapping rather than reporting only a list of
signatures. When expected results affect selection, diagnostics should show the
expected shape and each candidate's result shape.

Programmers must be able to discover:

- evaluation order;
- nontrivial default-expression work;
- copies, moves, and consumption;
- temporary lifetime extension;
- dynamic function-value dispatch or indirect calls;
- receiver evaluation;
- generated or unavailable candidate involvement;
- result construction and discard; and
- any future allocation, capture, async, or synchronization machinery used by
  the call.

Canonical formatting should preserve source argument order and use one space
after each comma. Named labels remain adjacent to `:`, and multiline arguments
use ordinary open-delimiter layout:

```zax
send(
    stream,
    encoding: utf8,
    trace: true
)
```

A formatter must not reorder arguments, replace named arguments with positional
ones, add or remove explicit transfer intent, or convert omission into
type-default initialization.

### Legacy tensions and provisional dispositions

The legacy corpus contains valuable intent but not one internally consistent
model:

- Ordinary `callee(...)` syntax, multiple results, function values, named
  prototype entries, defaults, and qualification-sensitive selection are useful
  continuing evidence.
- Construction's current cursor, evaluation, immediate-binding, and temporary
  rules are stronger than the older function prose and should constrain the
  reconstruction.
- Legacy member-function examples use unqualified member names inside bodies.
  Current declaration design supersedes that lookup behavior with explicit `_`.
- Legacy function-value compatibility says implementation parameter names may
  differ, while named calls require stable labels. The visible-prototype model is
  a candidate reconciliation.
- Legacy input `#` alternately means declared default, type default fallback, or
  placeholder. Separating omission, explicit type-default initialization, and
  result discard is more readable.
- Legacy optional-result examples imply important results can require capture.
  That intent is retained as a candidate, but exact omission and discard syntax
  is not recovered conclusively.
- Legacy result examples do not explain whether named result variables are
  default-initialized, caller-provided destinations, or delayed-construction
  slots. This is a semantic gap, not a formatting detail.
- Legacy operator material permits source- or import-order selection. That
  conflicts with current no-guessing direction and should not become ordinary
  callable precedent.
- Legacy automatic `last` selection based on later liveness conflicts with
  explicit cost and source-stability pressure when it affects semantics or
  overload selection.
- `mutator` functions introduce calls through value access or assignment. Such
  hidden invocation and cost need a separate, skeptical review rather than being
  assumed by the ordinary explicit-call model.
- Function composition (`>>`), invocation chaining (`|>`), input splitting,
  result combining, capture-based partial application, and variadics are
  higher-level argument/result transformations. They should not silently define
  fixed-arity ordinary invocation.
- Value-polymorphic function conditions execute runtime predicates as part of
  dispatch and include source-order proposals. They are a separate dispatch
  mechanism, not evidence for ordinary overload tie-breaking.

### Source-stability risks

Even with ambiguity diagnostics, overload evolution can change a successful
call:

- adding a more specific overload may change the selected body without creating
  an ambiguity;
- adding a default may make a previously nonviable candidate viable;
- renaming a parameter breaks named calls and may change candidate mapping;
- changing a parameter order changes the post-name positional cursor;
- changing result arity or discardability changes surrounding contexts;
- adding a result-only overload may make an inferred call ambiguous; and
- changing copy, move, `last`, or qualifier requirements may change both
  selection and source lifetime.

No local ranking rule can prevent all such API evolution hazards. Plausible
mitigations include explicit function-value binding to a prototype, parameter
label aliases, deprecation diagnostics, reflection over callable contracts, and
versioned compatibility tooling. These are future source-evolution mechanisms,
not reasons to use source-order fallback.

### Defensible deferrals

The fixed-arity synchronous model can proceed while preserving these later
questions:

- Complete closure layout, capture syntax, reassignment, raw function pointers,
  and recursive function-value representation remain function-value work.
- Generic inference, concepts, specialization, and generic result deduction
  remain generic work. They must eventually produce a concrete prototype before
  ordinary call binding completes.
- Variadic arguments, split/combine operators, spread, partial application,
  composition, and chaining remain variadic or function-composition work. They
  must specify how they produce ordinary fixed parameter and result slots.
- Complete copy, move, `last`, moved-from state, ownership, and destruction
  responsibility remain ownership and lifetime work. Ordinary calls require only
  explicit timing and no semantic transfer by optimization guess.
- Reference origin, borrow extension, place tracking, returned aliases, and
  temporary destruction order remain pointer and lifetime work. The current
  constraint is survival through synchronous call completion without automatic
  post-call escape.
- Async calls, cancellation, executors, and suspension remain async work. They
  need a completion concept broader than synchronous return.
- Runtime value-polymorphic dispatch, `mutator` access, arbitrary operator lookup
  domains, and module/import candidate priority remain their respective owners.
- Formal conversion ranking, diagnostic identifiers, ABI, lowering, and compiler
  data structures remain future specification or implementation concerns.

### Plausible lasting owners

This is a preliminary ownership sketch for later review, not a dry run or
authorization to create or edit owners:

- A current function concept owner would own ordinary call syntax, parameter
  mapping, defaults, result shapes, return completion, function-value invocation,
  overload viability/preference, and synchronous call completion.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  would continue to own declaration versus assignment and could own only the
  declaration-facing shape of result-capture bindings.
- [Zax qualifiers](../../../language/qualifiers.md) would continue to own
  qualification propagation and capability constraints, while the function
  owner would apply them at call selection and binding.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
  would retain construction packets, stored-member entries, and lifecycle
  operation details while referring to the general function owner for shared
  parameter defaults, overload selection, and call-boundary timing.
- Future pointer/lifetime owners would own post-call reference validity,
  temporary destruction order, transfer ownership, and async lifetime extension.
- A future operator owner would own operator lookup domains and operator-specific
  ranking while preserving the shared no-source-order and qualified-callable
  constraints.
- [Zax language-design terms](../../../language/terms.md) may eventually need
  concise definitions for callable, overload group, parameter slot, result
  shape, and call completion if those terms remain cross-cutting.

### Initial review questions answered below

These questions drove the conversational review recorded in the aligned
subsection that follows:

1. Should hidden default expressions run after all explicit source arguments, as
   proposed here, or in parameter order even when that interleaves them with
   explicit source effects?
2. Is `:` the intended explicit request for type-default parameter
   initialization, and is ordinary omission sufficient to request a declared
   parameter default without an input-side `#`?
3. Should result declarations be able to require caller capture, and if so must
   discards always be explicit?
4. What is the initialization model for named result bindings inside a function
   body?
5. Should expected-result context only filter candidates, or may conversion
   quality of results rank otherwise equal input matches?
6. Does the visible prototype, rather than the assigned implementation, own
   named-call labels for function values?
7. Is explicit source intent required whenever copy, move, or `last` can change
   overload selection or post-call source availability?

### Aligned findings after conversational review

This subsection records language-maintainer and agent alignment reached after
reviewing the initial reconstruction above. It supersedes earlier candidate
analysis wherever the two conflict. The earlier material remains in place as
provenance for the staged-before/unstaged-after review boundary.

These are aligned working findings, not accepted language design. No owner
document, legacy page, raw input, or source-structure rule changes through this
record alone.

#### Recognizable findings and promotion anchors

The short findings in this subsection preserve the plain meaning of the
conversation. The denser sections that follow supply rules, edge cases,
diagnostics, and dispositions. Promotion must preserve both the simple
programmer model and every material rule, diagnostic, cost, boundary, and
advanced behavior. It must organize that detail so ordinary use remains
teachable without forcing future specification work to rediscover accepted
behavior.

##### Clear intent earns flexibility

> Zax can provide flexible call forms when programmer intent remains clear.
> Ambiguous calls require the programmer to make the intended operation
> explicit.

```zax
value : Integer = parseValue(source) // explicit result intent
value := parseValue(source)          // ambiguous when only results differ
```

**Programmer meaning:** Labels, complete declarations, transfer markers, and
prototype bindings can unlock precise behavior. Zax does not use weighted
guessing or source order when two meanings remain plausible.

**Promotion consequence:** Teach the concrete disambiguation at the feature that
needs it. Do not turn this simple point into one exhaustive language-wide rule.

##### Whitespace presents intent

> Whitespace does not create Zax grammar, but it must honestly present the
> programmer's intent.

```zax
encoding: value   // label
encoding : Type   // declaration
```

In a label-capable position, source that could easily be a mistaken label or an
ungrouped declaration is an intent error:

```zax
send(stream, encoding : Encoding, bytes) // error
```

**Programmer meaning:** A spacing mistake produces a diagnostic rather than
silently changing the selected function or operation.

**Promotion consequence:** The call owner teaches the local distinction with
examples; the source-structure owner explains the general mandatory-intent rule.

##### Caller order comes first; callee defaults follow

> Explicit inputs run in caller-written order. Omitted defaults run afterward in
> the selected callee prototype's input-parameter order.

```zax
operation(
    third:,
    first: makeFirst(),
    second: makeSecond()
)
```

`makeFirst()` runs before `makeSecond()`. Only after all explicit
value-producing entries complete does the selected `operation` prototype
evaluate any omitted defaults in its own declared parameter order.

**Programmer meaning:** Visible caller effects remain left to right, while
dependent defaults remain deterministic.

**Promotion consequence:** Always name which order is meant: caller source-entry
order, selected-callee input order, selected-callee result order, member
lifecycle-plan order, or return-expression order.

##### Omission is not type-default initialization

> Omitting a parameter uses its declared default. Writing a type-default
> expression explicitly supplies a value at that exact source position.

```zax
send(stream, encoding:, bytes)   // declared parameter default, evaluated later
send(stream, encoding: :, bytes) // type-default Encoding, evaluated here
```

**Programmer meaning:** A caller can distinguish hidden contract behavior from
an explicitly ordered value-producing operation.

**Promotion consequence:** Teach these forms together and never describe one as
a fallback for the other.

##### Explicit result types improve API discoverability

> A complete explicit declaration may select a function by its result family.
> Inferred or recursively dependent expressions do not guess.

```zax
integer : Integer = parseValue(source)
floating : Float = parseValue(source)
text : String = parseValue(source)
```

This lets an API use one discoverable operation name rather than inventing
families such as `parseValueInt`, `parseIntegerValue`, or `intParseValue`.

```zax
value := parseValue(source) + parseValue(source) // ambiguous when results differ
```

**Programmer meaning:** The programmer can express a desired result where the
type is already known, but surrounding overloads and later uses do not create
recursive inference.

**Promotion consequence:** Preserve both the narrow rule and its substantial
developer benefit. Do not reduce it to an overload-resolution footnote.

##### A visible prototype can improve a call contract

> A compatible final function declaration may provide better labels, defaults,
> and result-acknowledgement policy while invoking the same implementation body.

```zax
operation final : (
    poorlyNamedResult : Widget
)(
    timeout : Duration = configuredTimeout()
) = {
    // One implementation body.
}

betterOperation final : (
    result # : Widget
)(
    expires : Duration = anotherConfiguredTimeout()
) = operation
```

Calls through `betterOperation` use `result`, `expires`, its `#` policy, and
`anotherConfiguredTimeout()`. The implementation body and its ordered slots do
not adapt.

**Programmer meaning:** A public call contract can improve naming and caller
defaults without copying or rewriting an implementation. If values must be
reordered, converted, synthesized, or reinterpreted, write a wrapper or lambda.

**Promotion consequence:** Teach this as callable-contract adaptation, not merely
as a trick for selecting an overload.

##### Results are output obligations, not automatic construction penalties

> A result starts as an output slot that must become complete. It does not pay
> for default construction unless the prototype asks for it.

```zax
make final : (result : Item)() = {
    return source // directly constructs result
}
```

```zax
makeDefault final : (result : Item = :)() = {
    result.name = "example"
} // completed result falls through
```

**Programmer meaning:** Direct return avoids hidden construction, while
preconstructed results remain concise when the body intends to mutate a ready
value.

**Promotion consequence:** Teach direct return first, then opt-in preconstruction
and explicit delayed `+++` tracking.

##### Multiple results are a sequence, not one hidden value

> A callable may produce an ordered result sequence. That sequence is not an
> implicit tuple, anonymous structure, or ordinary single expression.

```zax
number:, text: = produce()
consume(produce())
```

```zax
pair : Pair = produce() // error: several results are not one Pair
```

**Programmer meaning:** Results can be captured, routed, discarded, or forwarded
without pretending they are already one aggregate.

**Promotion consequence:** Preserve future room for an explicit operation that
promotes several results into one structural value.

##### Parentheses choose expression mode; mapping remains explicit

> Parentheses require one expression. A bare invocation in a mapping-capable
> position or contextual `=` may expose and route a result sequence.

```zax
consume(produce())        // bare result-sequence forwarding
consume((produce()))      // one expression
consume(: produce())      // one explicit positional expression
consume(: = produce())    // explicit result mapping
```

**Programmer meaning:** Parentheses are not cosmetic at a multiple-result
boundary. The source visibly chooses one value or result mapping.

**Promotion consequence:** Use many contrasting examples. Formatters must not
add or remove these parentheses as stylistic cleanup.

##### Comma and `\` express different continuation intent

> A comma continues an established list. `\` continues a newline that would
> otherwise matter. Writing both for the same newline is an intent error.

```zax
return \
    first: number:,
    second: text: = produce()
```

```zax
return \
    first: number:, \ // error: comma already continues this newline
    second: text: = produce()
```

**Programmer meaning:** Continuation punctuation is never defensive or
decorative; each physical newline has one reason for continuing.

**Promotion consequence:** The function owner shows return examples. The
source-structure owner defines the shared comma, `\`, indentation, blank-line,
and redundant-intent rules.

##### Implementation feasibility is valid design pressure

> This repository does not document compiler mappings, but parser feasibility and
> implementation complexity are legitimate pressures when choosing language
> behavior.

Expression grouping and result mapping therefore use mechanically distinct
contexts rather than requiring a parser to guess whether parenthesized contents
"look like" mapping.

**Programmer meaning:** The resulting source rules stay predictable and
tool-friendly.

**Promotion consequence:** Teach only the programmer-visible deterministic rule.
Keep parser and implementation reasoning in working material unless it explains
a real source constraint.

##### Pay only for behavior the contract adds

> Relabeling a statically known compatible function does not inherently require
> new runtime machinery. A captured default may add state or work because the
> programmer requested that behavior.

```zax
selected final : Prototype = implementation
```

The binding can remain a direct implementation selection when it adds no runtime
state. A delayed default that captures surrounding state may require that state
to be retained.

**Programmer meaning:** Cost follows requested behavior rather than syntax alone.

**Promotion consequence:** Explain discoverable costs without promising one
backend representation or byte-for-byte machine code.

##### Promotion teaches Zax rather than the design debate

> Promoted language documents teach the selected Zax model. Working records
> preserve the debate, rejected alternatives, and provenance.

Promotion should:

- begin with ordinary calls and progress toward advanced routing;
- introduce prerequisites before examples depend on them;
- use concrete Zax examples as the primary teaching tool;
- assume a competent general programmer;
- explain accepted surprising boundaries;
- omit rejected syntax that never became part of the language; and
- avoid organizing Zax around comparisons with another language.

Language-specific primers for C++, Rust, Java, or other programmers may be useful
later, but they are separate from the concept owner.

**Promotion consequence:** The dry run must test not only where facts belong but
whether the proposed owners can teach them in a coherent order.

##### Consequences do not disappear at a scope boundary

> A useful finding is not lost merely because its lasting owner lies outside
> `006`.

Every material finding receives one explicit disposition:

- promote it into an existing owner;
- preserve it in an appropriate indexed raw input;
- defer it with a reason and reopening pressure;
- reserve it for future specification or implementation work; or
- reject or supersede it deliberately.

**Programmer and maintainer meaning:** The dry run receives a complete input set
rather than only the findings that fit neatly into a future function page.

**Promotion consequence:** The consequence ledger near the end of this record
routes these recognizable findings; it does not replace them.

#### Programmer model and invocation phases

An ordinary synchronous invocation has distinct programmer-visible phases:

1. Discover candidates, map source entries, and select one available callable
   without executing runtime argument expressions.
2. Evaluate the callee and receiver operand once.
3. Evaluate every value-producing explicit input in source order and immediately
   bind or initialize its mapped parameter or input slot.
4. Evaluate declared defaults for still-unbound parameters in input-parameter
   declaration order in the selected callee's visible prototype and immediately
   bind each one.
5. Initialize opted-in result slots in result-slot declaration order in that
   visible prototype.
6. Enter and execute the callable body only after every parameter and opted-in
   result is complete.
7. Complete every declared result on each normal exit.
8. Map or transfer results into the surrounding result-consuming context.
9. Complete the synchronous call only after those transfers and every required
   immediate temporary lifetime have reached their specified boundary.

Static candidate analysis may inspect types, qualifications, labels, result
shapes, and declaration states. It must not execute runtime expressions while
deciding which candidate is selected.

For:

```zax
makeTarget().apply(first(), second())
```

the receiver expression `makeTarget()` evaluates once before `first()` and
`second()`. The explicit argument expressions then evaluate left to right, and
the body begins only after their parameter bindings and any omitted defaults are
complete.

#### Shared call and construction argument surface

Ordinary calls and construction packets use one general input model. A
construction packet adds only its stored-member category:

| Form | Meaning |
| --- | --- |
| `expression` | Positional value-producing input |
| `label: expression` | Named value-producing input |
| `: expression` | Explicitly positional value-producing input |
| `:` | Explicit positional omission; the mapped parameter must declare a default |
| `label:` | Explicit named omission; that parameter must declare a default |
| `: :` | Positional type-default expression evaluated at this source position |
| `label: :` | Named type-default expression evaluated at this source position |
| `.member = expression` | Construction-only stored-member input |

The construction packet therefore no longer needs a leading `:` on every
ordinary positional input:

```zax
value : Message = [{
    stream,
    encoding: localEncoding : Encoding,
    : backupEncoding : Encoding,
    .cache = cache
}]
```

This revises the current construction-packet surface. During authorized
promotion, update the construction owner to adopt the shared ordinary positional
form while preserving the construction-only stored-member distinction.

##### Adjacency and intent

In a source position where labels are possible:

- `name:` expresses label intent;
- `name : Type` expresses declaration intent;
- `name := value` expresses inferred-declaration intent.

A bare declaration expression at the top level of a label-capable input position
is an intent error even though the parser can recognize declaration-like tokens:

```zax
send(stream, encoding : Encoding, bytes)
// error: label or declaration-expression intent is unclear
```

The programmer may group the declaration expression or explicitly mark it
positional:

```zax
send(stream, (encoding : Encoding), bytes)
send(stream, : encoding : Encoding, bytes)
```

A named input already disambiguates an inner declaration expression:

```zax
send(stream, encoding: local : Encoding, bytes)
```

This intent rule is syntactic and contextual. It does not depend on whether a
current overload happens to declare a matching parameter label. Adding,
importing, removing, or renaming an overload must not change how the source is
parsed.

Whitespace that changes `encoding:` to `encoding :` therefore produces a
mandatory intent diagnostic rather than silently selecting another callable or
executing a different form.

#### Candidate-specific parameter mapping

Mapping is tested separately for every callable candidate:

1. The positional cursor starts at the first parameter.
2. A positional input maps the current parameter and advances the cursor.
3. A named input maps that label and resets the cursor to the parameter after it.
4. An explicit omission maps its parameter and advances or resets the cursor in
   the same way, but produces no value during the explicit-input phase.
5. A positional input never skips the current parameter merely because that
   parameter declares a default.
6. A parameter may be mapped at most once.
7. An unknown label, duplicate mapping, exhausted positional cursor, or
   unfulfilled required parameter makes the candidate nonviable.

Given:

```zax
configure final : ()(
    host : Host,
    retries : Integer = 3,
    verbose : Boolean
) = {
}
```

this is invalid:

```zax
configure(host, true)
```

`true` maps to `retries`; positional mapping does not skip that defaulted
parameter. The caller must acknowledge the omitted slot or use a later label:

```zax
configure(host, :, true)
configure(host, verbose: true)
```

Closely related overloads may map a later positional entry differently after a
named input. They remain legal only when viability and preference yield one clear
candidate. Source or declaration order never resolves an equal set.

#### Explicit inputs, omissions, and declared defaults

Explicit omission and type-default initialization are separate operations:

```zax
send(stream, :, bytes)
send(stream, encoding:, bytes)
```

Both empty entries request the mapped parameter's declared default. The candidate
is nonviable if that parameter has no declared default.

```zax
send(stream, : :, bytes)
send(stream, encoding: :, bytes)
```

Here the inner `:` is an actual expression requesting default initialization of
the selected parameter type. It evaluates at that source position and overrides
any declared parameter default.

The forms therefore distinguish:

```zax
send(stream, bytes: data)        // implicit omission
send(stream, encoding:, data)    // explicit omission
send(stream, encoding: :, data)  // explicit type-default value
```

Implicit and explicit omission have identical runtime semantics. The placeholder
affects mapping and documents intent but does not pull hidden default work into
its source position.

##### Evaluation order

Every value-producing explicit entry evaluates in caller source-entry order and
immediately binds its mapped slot. Empty omission entries perform mapping but no
evaluation. After all explicit entries complete, declared defaults for
still-unbound parameters evaluate in input-parameter declaration order in the
selected callee's visible prototype.

For:

```zax
send(
    stream: giveMeAStream(),
    :,
    bytes: giveMeData()
)
```

the order is:

1. evaluate `giveMeAStream()` and bind `stream`;
2. acknowledge but do not evaluate the omitted parameter;
3. evaluate `giveMeData()` and bind `bytes`;
4. evaluate the omitted parameter's declared default;
5. enter `send`.

For:

```zax
send(
    stream: giveMeAStream(),
    : :,
    bytes: giveMeData()
)
```

the type-default expression is explicit, so it evaluates between
`giveMeAStream()` and `giveMeData()`.

##### Dependent defaults

A parameter default may refer to the receiver operand, applicable surrounding
callable state, and earlier parameters in prototype order. It may not refer to
itself or a later parameter.

```zax
operation final : ()(
    first : A,
    second : B = makeB(),
    third : C = makeC(second)
) = {
}
```

All explicit values bind before either omitted default. If both defaults are
needed, `second` initializes before `third`, so `third` observes the completed
`second`.

A named omission written before an explicit `second` does not force early default
evaluation:

```zax
operation(
    third:,
    first: myA,
    second: myB
)
```

The explicit `myA` and `myB` bindings complete first. The later default phase
then initializes `third` from the explicitly supplied `second`.

#### Construction argument order and member construction order

Construction retains separate input and member phases.

```zax
MyType :: type {
    first : A
    second : B
    third : C
    fourth : D

    +++ final : ()(
        first : A,
        second : B = makeB(),
        third : C
    ) = {
        _.second.+++(second)
        _.third.+++(third)
        _.first.+++(first)
    }
}

value : MyType = [{
    third: myC,
    .fourth = myD,
    first: myA,
    second:
}]
```

The packet evaluates `myC`, `myD`, and `myA` in source order, then evaluates the
declared default for `second`. The stored-member input for `fourth` is already
ready when the member construction plan begins.

Because `first`, `second`, and `third` have explicit body construction, the
member construction order is:

1. construct `fourth` from the call-site member input before the body;
2. enter the body;
3. explicitly construct `second`;
4. explicitly construct `third`;
5. explicitly construct `first`.

Argument evaluation order and member construction order are independent
programmer-visible sequences.

#### Declaration expressions and scope

An explicit argument may introduce a binding:

```zax
send(
    : temporary : Buffer,
    inspect(temporary)
)
```

The binding becomes visible after its initialization completes. Later explicit
arguments in the same call may use it. It survives through call completion and
does not escape into the following statement merely because it has a name.

Grouping remains available when a declaration expression would otherwise be
unclear:

```zax
send((temporary : Buffer), inspect(temporary))
```

Declared defaults belong to the callable contract's source context and cannot
refer to a caller-local argument declaration.

#### Visible callable contracts and function values

Parameter labels and declared defaults belong to the visible callable prototype
used at the call site.

```zax
callback : ()(
    timeout : Duration = configuredTimeout()
) = implementation
```

Calls through `callback` use:

- `callback`'s parameter label;
- `callback`'s declared default;
- `callback`'s parameter and result qualifications; and
- `implementation`'s compatible executable body.

Labels and defaults declared on `implementation` remain part of
`implementation`'s own direct call contract. Assignment does not transplant them
into `callback`.

A default expression evaluates once per actual omission at the call boundary.
Any captured environment or stored state needed by that delayed expression is a
cost of the visible call contract.

Calling a resolved function value and invoking a selected declaration use the
same call surface:

```zax
declaredFunction(input)
functionValue(input)
instance.memberFunction(input)
```

A resolved function value supplies one prototype. It does not dynamically search
declarations that happen to share its source name. An overload group must first
select one compatible declaration.

An exact prototype binding can make that selection explicit:

```zax
selected final : ()(
    value : Document readonly &
) = consume

selected(document)
```

This is an ordinary final function binding and no separate alias mechanism is
currently justified. When the initializer is a statically known declaration,
the selected implementation is fixed, and the new prototype adds no runtime
state, the compiler may direct-call without materializing another function-value
slot. `final` alone is not a universal no-storage guarantee; evaluated closures
or captured defaults may still require representation.

##### Adapting the visible contract without adapting the implementation

An exact compatible prototype binding can also provide a different
programmer-facing call contract over the same implementation:

```zax
operation final : (
    uglyAndPoorlyChosenName : Widget
)(
    timeout : Duration = configuredTimeout()
) = {
    // One implementation body.
}

altOperation final : (
    result : Widget
)(
    expires : Duration = anotherConfiguredTimeout()
) = operation

altOperationWithDiscardResult final : (
    result # : Widget
)(
    first : Duration
) = operation
```

Calls through `altOperation` use the labels `result` and `expires` and the
default `anotherConfiguredTimeout()`. Calls through `operation` continue to use
`uglyAndPoorlyChosenName`, `timeout`, and `configuredTimeout()`. Defaults are
replaced by the visible contract rather than layered; omitting `expires` does
not subsequently evaluate `configuredTimeout()`.

Result omission permission likewise belongs to the visible contract.
`altOperationWithDiscardResult` permits callers to omit its `result`, but the
unchanged implementation still produces the complete `Widget` result.

Direct compatibility is positional over the ordered input and result slots. The
new prototype may relabel compatible slots, replace call-boundary defaults, and
change caller result-acknowledgement policy. It may not require the implementation
body or its ordered slots to reorder, convert, synthesize, or reinterpret
values. If entering the original body requires executable adaptation, write a
wrapper body or lambda instead of assigning the implementation directly.

This is a programmer-visible semantic compatibility requirement, not a promise
of byte-for-byte identical generated machine code. The same implementation body
is invoked without a semantic adapter; any additional work or stored environment
comes only from call-boundary behavior added by the visible contract, such as a
capturing default expression.

Invoking a provably default-`Nothing` function binding remains a diagnostic.
Otherwise unhandled invocation of that default state panics according to the
current declaration owner; it does not become a resultless no-op.

#### Result slots and pre-body initialization

A result declaration establishes:

- an ordered result slot;
- its source-facing label, type, and qualifications; and
- an obligation to contain one complete value on every normal exit.

An uninitialized result slot has storage and a tracked obligation but no live
value:

```zax
make final : (result : Item)() = {
    return source
}
```

`return source` directly initializes the result slot without a preceding default
constructor penalty.

A result may opt into construction before the body:

```zax
make final : (result : Item = :)() = {
    result.name = "example"
    result.value = 42
}
```

The `= :` initializer requests type-default construction. A general initializer
directly constructs from its expression:

```zax
make final : (
    result : Item = seed
)(
    seed : Seed
) = {
    result.finishInitialization()
}
```

Result initializers run after all parameters and omitted defaults are bound, in
result-slot declaration order in the selected callee's visible prototype, before
body entry. They may refer to any completed input parameter even though result
declarations appear syntactically before input declarations:

```zax
make final : (
    result : Item = makeItem(input)
)(
    input : Input
) = {
}
```

An initially unconstructed result may instead be constructed conditionally or
later:

```zax
make final : (result : Item)() = {
    result.+++(source)
    return
}
```

Ordinary `=` never changes meaning according to tracked construction state. It
requires an already live destination and remains ordinary operator selection.

The compiler tracks result construction through control flow and rejects use
before construction, duplicate construction of one live result lifetime, and
normal completion with any incomplete result.

#### Return completion is all-results or no-results

A value-bearing return supplies the complete declared result shape:

```zax
return makeFirst(), makeSecond()
```

It requires every result slot supplied by that list to be unconstructed on that
path. Expressions evaluate left to right and immediately establish their
corresponding slots.

A bare return supplies no result expressions:

```zax
return
```

It is valid only when every declared result slot is already complete.

Falling through the closing brace is equivalent to a bare return. It is valid
only when every result obligation is complete:

```zax
make final : (result : Item = :)() = {
    result.name = "example"
}
```

Partial value-bearing result lists are unavailable. A function either supplies
the complete result shape through the return list or completes every named slot
and uses bare return or fallthrough.

Paths may use different complete mechanisms:

```zax
make final : (result : Item)() = {
    if condition {
        result.+++(first)
        return
    }

    return second
}
```

`return result` is not the canonical completion of an already constructed output
slot. It would suggest a transfer from the slot into itself. Use bare return or
fallthrough.

#### Result shapes, labels, and caller acknowledgement

A selected prototype declares an ordered result shape:

- `()` is zero results;
- `(value : T)` is one result;
- `(first : A, second : B)` is two results.

Zero results are not a hidden `Nothing`, unit value, or implicit receiver result.
A resultless call is legal as a statement but cannot satisfy a value-required
expression or mapping slot.

Result names are source-facing labels. Prototype declarations use ordinary
declaration spacing:

```zax
produce final : (
    number : Integer,
    text : String
)() = {
    return 42, "answer"
}
```

At a label-capable result-routing site, adjacency expresses label intent:

```zax
number:, text: = produce()
```

The labels must match the selected result declaration. This shorthand selects
the labeled results and introduces same-named inferred caller bindings.

This is an intent error:

```zax
number :, text: = produce()
```

The first entry neither supplies a complete typed declaration nor expresses
adjacent label intent.

Changing a public result label may therefore break source that routes by that
label. Result labels and parameter labels are both source-facing contract
elements even when compatible function-value implementations use different
local names.

A result may be marked as permitting silent omission. Retaining the legacy `#`
marker provisionally:

```zax
measure final : (
    requiredValue : Integer,
    diagnostic # : String
)() = {
}
```

The producer still completes both results. The marker affects caller
acknowledgement:

```zax
value := measure()    // trailing diagnostic may be omitted
value:, # = measure() // explicit acknowledgement and discard
measure()             // error: requiredValue was not acknowledged
```

An explicit caller-side `#` consumes and discards one result. An unmarked result
must be captured, routed, or explicitly discarded; a declaration-side `#`
permits it to remain unmentioned. Short capture consumes a prefix, and every
unmentioned trailing result must permit omission.

#### Expression mode and result-mapping mode

Zax distinguishes a one-value expression from a bare callable result sequence.

| Form | Mode |
| --- | --- |
| `consume(produce())` | Bare invocation in a mapping-capable argument-list position; may expose a result sequence |
| `consume((produce()))` | Parenthesized expression; must form one usable value |
| `consume(: produce())` | Explicit positional expression entry; must form one usable value |
| `consume(: = produce())` | Contextual result mapper from the producer's bare result sequence |
| `consume(: result: = produce())` | Result mapper beginning from the labeled producer result |
| `consume(: = (produce()))` | Mapper whose right side is one parenthesized expression value |

Parentheses create an intentional arity boundary. A bare multi-result invocation
inside grouping parentheses must reduce to one expression result; several
mandatory results cannot become one value merely through grouping.

A formatter must not add or remove such parentheses cosmetically:

```zax
consume(produce())
consume((produce()))
```

may have different semantics.

The contextual `=` activates result mapping. It establishes source and
destination cursors and maps a bare result sequence rather than requiring one
expression value.

```zax
consume(: = produce())
```

starts at the current destination parameter and the producer's first result.

```zax
consume(: result: = produce())
```

selects `result`, maps it to the current destination, then continues sequentially
through later producer results and later destination parameters. Results skipped
before the selected source label must permit omission or receive another explicit
disposition.

Parentheses on the mapper's right side force expression mode before mapping:

```zax
consume(: = (produce()))
```

This can map one expression value but cannot recover the producer's bare
multiple-result sequence.

Call argument-list delimiters and grouping parentheses are mechanically
different contexts. `consume(...)` accepts argument and result-routing grammar.
An otherwise standalone `(...)` groups one expression. Construction packets
accept their construction input and mapping grammar. Naked `return` accepts a
return-result list and routing grammar.

#### Result capture, remapping, and existing destinations

Complete inferred capture may use matching result labels:

```zax
number:, text: = produce()
```

Complete explicit capture supplies declared destination types:

```zax
number : Integer, text : String = produce()
```

A complete explicit declaration shape may also participate in the narrow
expected-result model described below. A partially inferred shape cannot.

New declarations and existing destinations may coexist:

```zax
number:, existingText = produce()
```

`number:` selects the matching result label and introduces a binding.
`existingText` consumes the next positional source result and performs ordinary
assignment into an existing destination. These operations are not atomic;
earlier initialization or assignment effects remain observable if a later
destination panics.

Different labels may be remapped explicitly:

```zax
consume(
    first: number:,
    second: text: = produce()
)
```

The first label in each pair names the consumer destination. The second names the
producer result.

Same-name routing may use a shorthand:

```zax
consume(text:, number: = produce())
```

Conceptually this is equivalent to routing `produce.text` to `consume.text` and
`produce.number` to `consume.number`.

Named and positional destination mapping may mix:

```zax
consume(
    first: number:,
    : text: = produce()
)
```

The named destination resets the destination cursor after `first`; the explicit
positional destination then uses that cursor. Named source selection likewise
resets the source-result cursor after the selected result.

##### Typed intermediates

A spaced colon introduces a declaration expression rather than selecting a
source label:

```zax
consume(
    first: number:,
    second: text : String = produce()
)
```

`text : String` consumes the current positional source result. It does not select
the result labeled `text`.

No aligned one-entry syntax both selects a differently named source result and
constructs a differently typed intermediate. Use two explicit steps:

```zax
number:, text: = produce()

consume(
    first: number,
    second: widget : Widget = text
)
```

The two-step form is the aligned programmer-facing operation and should be taught
directly without introducing unsupported one-entry alternatives.

##### Anonymous typed declarations

An explicit complete anonymous declaration can select a result overload while
remaining one expression input:

```zax
anotherFunction(: : Integer = produce())
anotherFunction(: (: Integer = produce()))
anotherFunction((: Integer = produce()))
anotherFunction(# : Integer = produce())
```

These are current working forms for invocation analysis. Canonical formatting may
prefer one later, but the promoted function documentation must explain the escape
variations because grouping and explicit positional intent affect mapping mode.

#### Result-routing groups and two cursors

Each contextual `= producer()` establishes one result-routing group:

1. Evaluate the producer exactly once.
2. Complete its declared result slots.
3. Establish or reset the source-result and destination-parameter cursors from
   the written routing prefix.
4. Route or discard results in routing-entry order.
5. Continue sequential mapping while source results and destination slots remain.
6. Require every unconsumed source result to permit omission or receive an
   explicit discard.
7. Proceed to the next outer argument or routing group.

For:

```zax
consume(
    first: number:,
    : text: = before(),

    third:,
    fourth: = produce(),

    fifth:,
    #: # = after()
)
```

`before()`, `produce()`, and `after()` each execute once in source order. Each
group completes and routes its results before the next producer begins.

Writing `before()` again performs another invocation. Two textual calls never
share one evaluation automatically. A producer that must execute once needs one
routing group or an earlier explicit capture.

##### Source and destination discard

In:

```zax
consume(
    #: #,
    first: number:,
    second: text: = produce()
)
```

the outer `#:` supplies no consumer destination and invalidates the destination
cursor. The inner `#` consumes and discards the current source result and advances
the source cursor. A later named destination reestablishes the destination
cursor.

Each source result may be consumed at most once, and each consumer destination
may be initialized or bound at most once.

##### Cursor exhaustion and optional tails

Simple or explicit mapping consumes producer results while compatible consumer
destinations remain. A result marked as permitting omission is still mapped when
a destination remains; optionality does not make the mapper skip it to improve a
later match.

If a producer has three results and only two destinations remain, the third may
remain unused only when it permits omission.

For:

```zax
consumeFour(
    produceThreeWithLastOptional(),
    produceTwoMore()
)
```

the mapper consumes all three results from the first producer because
destinations remain. It then consumes the first result from the second producer
into destination four. The second producer's remaining result must permit
omission or the call is invalid. The mapper does not backtrack and drop the first
producer's optional result to make room.

#### Direct forwarding to calls, returns, and construction

A bare invocation in a mapping-capable argument-list position may feed several
consecutive parameters:

```zax
consume(produce())
```

Result names do not automatically remap input names. Simple forwarding is
positional. Use an explicit routing group to reorder by labels.

A named argument maps one parameter and therefore requires one expression value:

```zax
consume(payload: produceTwo())
// error when produceTwo cannot form one usable expression value
```

##### Return forwarding

Naked return accepts the same bare sequence and explicit result-routing model:

```zax
return produce()
```

may forward a compatible complete result sequence into the enclosing function's
result slots.

```zax
return outputText: text:, outputNumber: number: = produce()
```

explicitly remaps producer result labels into enclosing result labels.

Grouping parentheses always select expression mode. They never preserve or
introduce return-result mapping:

```zax
return (produce())
```

requires one expression value.

```zax
return (
    outputText: text:,
    outputNumber: number: = produce()
)
// error: result routing cannot be grouped as an expression
```

##### Construction forwarding

A construction packet may route several results as constructor inputs:

```zax
pair : Pair = [{
    produceTwo()
}]
```

This selects a compatible `Pair` constructor. It does not automatically pack
result labels into stored members or create one anonymous structural value.
Future explicit structural promotion may consume several results into one value,
but ordinary invocation and construction must not conflate those operations.

#### Narrow expected-result selection

Result context participates only at a direct, complete, explicitly typed
declaration boundary. Applicable contexts include:

- a fully typed local declaration;
- a fully typed declaration expression;
- a complete explicitly typed multiple-result capture;
- a result initializer; and
- a value-bearing return against a complete enclosing result prototype.

```zax
integer : Integer = parseValue(source)
floating : Float = parseValue(source)
```

These declarations may select input-equivalent `parseValue` overloads whose
declared result families match the explicit destination.

This provides no expected result:

```zax
inferred := parseValue(source)
```

Nor do later use, ordinary assignment, unresolved operators, or an outer
overloaded consumer create one:

```zax
value = a + parseValue(source)
value := parseValue(source) + parseValue(source)
consume(parseValue(source))
```

The inner call must resolve independently unless source explicitly introduces a
complete typed declaration or result-routing label constraint.

##### Narrow family and transfer matching

The expected declaration considers:

- result arity;
- declared base value type;
- qualifications;
- indirection or reference shape; and
- same-family direct transfer viability.

For:

```zax
value : Integer = makeRandom()
```

results declared as `Integer`, qualified `Integer`, or `Integer &` may be
same-family candidates subject to direct copy, move, reference, and qualification
rules. Their relative quality belongs to the deferred result-precedence table.

A `Widget` result does not participate merely because an `Integer` constructor
could accept `Widget`. Result selection never searches the destination's
arbitrary converting-constructor graph.

The programmer may make the conversion boundary explicit:

```zax
widget : Widget = (: Integer = makeRandom())
```

The inner anonymous `Integer` declaration selects the intended result family.
Ordinary `Widget` construction then consumes that completed `Integer`.

This reflects a general design stance: Zax permits flexible overloads and mapping
only while the source expresses one clear intent. When plausible interpretations
become incomparable or recursive, compilation stops and requires a label,
complete declaration, explicit transfer, or another visible disambiguation.

##### Major programmer-facing value

Restricted result-directed selection allows one coherent operation family:

```zax
integer : Integer = parseValue(source)
floating : Float = parseValue(source)
byte : Byte = parseValue(source)
text : String = parseValue(source)
```

Without it, API authors and callers must invent and discover unrelated naming
schemes such as `parseValueInt`, `parseIntegerValue`, or `intParseValue`. The
selection rule is intentionally narrow, but its developer-facing discoverability
and naming benefits are substantial.

#### Overload viability and partial-order preference

Candidate selection separates viability from preference.

A candidate is viable only when:

- every explicit input and omission maps without duplication;
- every required parameter receives an explicit value or declared default;
- argument, receiver, source-result label, and expected-result requirements are
  compatible;
- required copy, move, `last`, reference, pointer, and qualification operations
  are available;
- every required result receives a valid disposition in the surrounding context;
  and
- the declaration is available for invocation.

The best match is determined by a partial order, not mismatch counting or a
universal score.

For every comparable receiver, input, and applicable result slot, a candidate
relation is:

- better;
- equal;
- worse; or
- incomparable.

Candidate A dominates B only when A is no worse in every applicable comparison
and strictly better in at least one. Crossed advantages remain ambiguous:

```text
A: exact, conversion
B: conversion, exact
```

Neither dominates the other.

The per-slot better relation must be asymmetric, transitive, and acyclic. It need
not be total. Copy versus readonly reference, move versus `last`, or another
semantically different binding may remain incomparable rather than receiving an
arbitrary language-wide priority.

After supplied receiver and input bindings compare equally, a candidate requiring
fewer omitted callable-parameter defaults is better. This count includes only
declared callable defaults activated by omission, not nested constructor,
conversion, member, result, or explicit type-default work.

Result quality may break a tie only when:

- receiver and supplied-input comparisons are equal;
- a permitted complete declaration context supplies the expected shape; and
- the same-family result relation is clear.

A candidate with better inputs wins without trading that advantage against a
different candidate's better result, provided its result remains viable in the
context.

The detailed per-slot type, qualification, indirection, copy, move, reference,
pointer, and `last` precedence table is explicitly deferred. The partial-order
structure and ambiguity behavior are not deferred.

No tie is resolved by source, declaration, import, module, or generation order.
An equal undominated set is an ambiguity error.

Availability remains separate from match quality. A uniquely best bodyless,
forbidden, unresolved generated-versus-existing, or otherwise unavailable
candidate produces an unavailable-operation diagnostic. Selection does not
silently fall through to a weaker candidate.

Explicit labels, transfer qualifications, casts, typed declarations, or exact
prototype bindings provide source-level disambiguation.

#### Immediate transfer, callbacks, and call completion

For every value-producing input:

1. evaluate its expression;
2. immediately copy, move, consume through `last`, bind a reference or pointer,
   or perform its other selected parameter initialization;
3. complete the observable effects of that binding before the next explicit
   input begins.

```zax
source : Integer = 1

observe(
    source,
    action: changeSourceToTwo(source)
)
```

A copied first parameter captures `1`. A reference binds while the source is `1`
but may observe `2` in the body. A move or `last` transfer occurs before
`changeSourceToTwo` and may make that later input invalid.

Whenever copy, move, or `last` changes semantics, source availability, or overload
selection, source intent must be explicit. Whole-program liveness may justify
optimization only after the semantic operation is known; adding or removing a
later use must not silently change call meaning.

A callback invoked while evaluating an argument completes before the next
argument starts. A callback invoked by immediate parameter construction is part
of that binding step. Reentrancy may observe completed earlier effects but not
fictional later parameter state.

#### Temporary lifetime

A temporary bound to a reference parameter survives through synchronous call
completion:

```zax
inspect(makeBuffer())
```

If `inspect` receives `Buffer readonly &`, the `Buffer` temporary remains live
while `inspect` executes and its results map into the surrounding context.

That does not authorize a returned reference to escape:

```zax
view := returnView(makeBuffer())
```

The applicable lifetime strategy must reject an escaping reference into the
temporary, establish an owning or extending mechanism, or require an explicit
unsafe operation.

For nested mapping:

```zax
consume(produce())
```

the producer's result slots survive until their transfers or bindings into
`consume` complete. A temporary result bound to an outer reference parameter
survives through the outer call. Exact caller temporary destruction order,
returned alias validity, moved-from states, and ownership disposition remain
future lifetime work.

Immediate synchronous callbacks and reentrancy are inside call completion.
Async suspension requires a broader completion and lifetime model and remains
deferred.

#### Comma-list continuation and redundant `\`

The multiple-result model creates a concrete source-structure consequence.

At statement level, a comma already recognized as a separator in a comma-list
implicitly continues that list across the following physical newline:

```zax
return first: number:,
    second: text: = produce()
```

This does not generalize to arbitrary operators:

```zax
value := first +
    second // error: `+` does not continue the statement
```

When the first result begins on the line after an otherwise complete bare
`return`, explicit continuation remains necessary:

```zax
return \
    first: number:,
    second: text: = produce()
```

The comma carries the next newline, so no `\` is needed after it.

A continuation-only line may visibly carry the list across one additional
physical newline:

```zax
return \
    first: number:,
    \
    second: text: = produce()
```

A blank or comment-only line does not carry continuation.

Each physical newline has exactly one continuation reason:

1. an open delimiter already permits it;
2. a recognized comma-list separator continues it; or
3. explicit `\` suppresses an otherwise significant newline.

Using `\` where an open delimiter or comma already continues the newline is a
mandatory redundant-intent error:

```zax
return \
    first: number:, \ // error: comma already continues this newline
    second: text: = produce()
```

```zax
consume(
    first, \ // error: open `(` already permits this newline
    second
)
```

Continuation lines must retain truthful hanging indentation. A formatter must
not silently repair invalid redundant continuation.

This aligned consequence revises the current explicit-continuation owner and
must be incorporated there during promotion.

#### Diagnostics, formatting, and visible costs

Invocation diagnostics should distinguish at least:

- no callable found;
- default-`Nothing` function invocation;
- unknown parameter or result labels;
- ambiguous label-versus-declaration intent;
- duplicate parameter, result, source-result, or destination mapping;
- exhausted or invalid positional cursors;
- a missing required parameter or result disposition;
- use of omission where no declared parameter default exists;
- type-default expression versus declared-default omission;
- argument, receiver, result, qualification, or transfer mismatch;
- a zero-result call in a value-required context;
- several mandatory results forced into one expression;
- parenthesized expression mode where result mapping was intended;
- a result-routing construct incorrectly grouped as an expression;
- an unavailable uniquely best candidate;
- incomparable undominated candidates;
- result-context selection without a complete declaration;
- use after an earlier move or `last` transfer;
- incomplete or duplicate result construction;
- a returned reference escaping a temporary;
- redundant explicit continuation; and
- contradictory continuation indentation.

When candidate mappings differ, diagnostics should show candidate-specific
parameter and result routing rather than only signatures. When result context
participates, diagnostics should show the explicit expected shape and why each
candidate is exact, same-family compatible, incompatible, unavailable, or
incomparable.

Programmers must be able to discover:

- callee, receiver, explicit-input, default, result-initializer, body, return, and
  routing order;
- hidden declared-default work;
- copies, moves, `last`, references, pointers, and temporary extension;
- indirect function-value calls and stored default-expression environments;
- result construction, remapping, omission, and discard;
- generated or unavailable candidate involvement; and
- future allocation, capture, async, or synchronization machinery used by a
  call.

Canonical formatting preserves:

- source order of value-producing entries and routing groups;
- adjacency for labels;
- spacing for declarations;
- explicit positional intent;
- omission versus type-default expressions;
- parentheses that establish expression mode;
- contextual `=` mapping boundaries;
- result and destination labels;
- explicit transfer intent;
- comma-list continuation; and
- one continuation reason per physical newline.

Formatters must not reorder entries, replace named mapping with positional
mapping, add or remove semantically meaningful parentheses, convert omission
into type-default initialization, or add or remove transfer semantics.

#### Source-stability consequences

Even deterministic ambiguity diagnostics cannot prevent every API evolution
hazard:

- adding a more specific overload may change a selected body;
- adding a default may make another candidate viable;
- renaming a parameter or result label breaks labeled source;
- reordering parameters or results changes positional cursors;
- changing result arity or discardability changes surrounding mappings;
- adding a result-only overload may make an inferred call ambiguous;
- changing copy, move, reference, pointer, `last`, or qualification requirements
  changes viability, preference, cost, and post-call source availability; and
- adding or removing parentheses at a result boundary can change expression mode
  versus result mapping.

No ranking rule can prevent all such changes. Explicit prototype bindings, label
aliases, deprecation diagnostics, contract reflection, and versioned
compatibility tooling remain possible mitigations. Source-order fallback is not.

#### Promotion teaching constraint

Promotion must preserve the recognizable findings and the materially important
precision behind them. Rules, diagnostics, costs, corner cases, advanced
behavior, and future-specification pressure belong in current documentation when
they affect programming or prevent later work from having to rediscover the
design. They should be layered under clear mental models, examples, advanced
behavior, diagnostics, or reference-detail sections rather than compressed into
unreadable teaching prose.

The dry run must test whether each proposed owner can teach its part in a
progressive order with practical examples. Rejected alternatives remain in this
working record when they only explain how the design was discovered. An
accepted, likely-to-surprise boundary may still require a counterexample in
programmer-facing documentation.

#### Legacy dispositions and owner impacts

The aligned model retains useful legacy intent while rejecting or revising
inconsistent details:

- ordinary `callee(...)`, first-class function values, multiple results, defaults,
  qualified parameters/results, and result-sensitive declarations remain useful;
- construction's strict source order, immediate binding, cursor behavior, and
  temporary requirements become shared invocation rules;
- leading `:` on every construction positional input is revised into optional
  explicit positional intent;
- stored-member `.member = expression` remains construction-only;
- unqualified member lookup inside legacy function bodies remains superseded by
  explicit `_`;
- input-side `#` no longer conflates declared defaults, type defaults, and result
  discard;
- result slots are tracked output obligations rather than always
  default-constructed body locals;
- current positional `name:` result-capture wording in the declaration owner must
  change to source-result label semantics and explicit remapping;
- source/import-order overload fallback is rejected;
- automatic semantic `last` selection from later liveness is rejected;
- hidden `mutator` invocation, runtime value-polymorphic dispatch, composition,
  chaining, variadics, and split/combine operators do not define ordinary
  fixed-arity calls; and
- grouping parentheses are not cosmetically interchangeable with a bare
  multi-result invocation.

Likely lasting owner impacts, to be tested by the later dry run, include:

- a function concept owner for ordinary invocation, callable contracts,
  parameter mapping/defaults, result slots, return completion, result routing,
  overload structure, and synchronous call completion;
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) for
  declaration-facing result captures, existing destinations, and the revised
  label/declaration boundary;
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
  for revised packet punctuation, construction-only stored-member entries,
  member lifecycle plans, and use of shared call behavior;
- [Zax source structure](../../../language/source-structure.md) for comma-list
  continuation, redundant `\`, grouping-expression boundaries, and mandatory
  indentation intent;
- [Zax qualifiers](../../../language/qualifiers.md) for per-layer parameter,
  receiver, result, reference, and pointer constraints;
- future pointer/lifetime owners for post-call alias validity, temporary
  destruction order, move/`last` ownership, and async lifetime extension;
- a future operator owner for operator lookup domains while preserving shared
  partial-order and no-source-order constraints; and
- [Zax language-design terms](../../../language/terms.md) for cross-cutting terms
  such as callable, parameter slot, result slot, result shape, result-routing
  group, and call completion if they remain broadly useful.

This ownership list is preliminary working analysis, not the pre-promotion
documentation fit dry run.

#### Consequence and disposition ledger

This ledger preserves material findings discovered through `006` even when
their lasting owners or future reviews are outside ordinary invocation. The
later dry run must account for every row; it may refine the proposed owner but
must not drop the finding merely because it crosses the initiating boundary.

| Finding or pressure | Current disposition before the dry run |
| --- | --- |
| Flexible call forms are available when intent is clear; ambiguous calls require explicit disambiguation | Preserve through the concrete function examples; test whether a concise cross-cutting form also belongs in the language vision |
| Promotion must teach progressively through examples rather than reproduce working findings | Preserve as `006` promotion guidance; test whether the general guidance warrants an update to [documentation architecture](../../documentation.md) |
| Language-specific primers such as guidance for C++, Rust, or Java programmers are useful only after enough Zax concepts are accepted | Preserve through the indexed [language-similarity input](../../raw/similarity.md); do not turn the function owner into a comparison guide |
| Shared ordinary call and construction positional syntax, with `.member = expression` remaining construction-only | Promote coherently through the function and [construction](../../../language/construction-and-destruction.md) owners |
| Result labels, label/declaration adjacency, mixed capture, and declaration-expression scope revise current declaration-facing behavior | Promote coherently through the function and [declaration](../../../language/declarations-and-bindings.md) owners |
| Comma-list continuation, one continuation reason per newline, grouping-expression boundaries, and mandatory intent indentation | Promote through [source structure](../../../language/source-structure.md) in the same coherent change set |
| Adapted visible prototypes may relabel compatible slots, replace defaults, and change result acknowledgement without adapting the implementation body | Preserve in `006` and promote through the function-value and callable-contract owner |
| Parameter and result label changes are source-contract changes; aliases, deprecation windows, reflection, and compatibility tooling may mitigate evolution | Preserve as an explicit function/source-evolution deferral until the dry run identifies a lasting compatibility or tooling owner |
| A slot intentionally unavailable to named routing needs an eventual anonymous/private-label form | Preserve as deferred function syntax; do not infer a spelling from current anonymous declaration or discard markers |
| Several currently valid anonymous typed declaration escapes need canonical formatting without losing their distinct parsing roles | Preserve in function documentation and formatter consequences; canonical preference remains later syntax/formatting refinement |
| A future explicit operation should promote a multiple-result sequence into one anonymous structural value | Preserve in `006` and route to the existing indexed [structural-typing input](../../raw/structural-typing.md); do not reinterpret ordinary mapping or construction as packing |
| Same-family value, qualified value, reference, pointer, copy, move, and `last` result/input relations need an acyclic precedence table | Preserve the established partial-order structure in `006`; defer the detailed table to function, qualifier, and lifetime work |
| Temporary survival, returned-reference escape, moved-from state, source authority, and resource disposition require complete lifetime rules | Preserve current call-boundary constraints and route detailed work through the indexed [lifetime input](../../raw/lifetimes.md) |
| Proof of result completion, use-after-transfer, alias escape, lifecycle validity, and unsafe override provenance require later analysis and safety mechanisms | Preserve diagnostics and constraints here; route future controls through indexed [analysis-controls](../../raw/analysis-controls.md) and [safety](../../raw/safety.md) inputs |
| Async suspension, cancellation, delayed destinations, and post-initiation callbacks require a broader completion boundary | Preserve the synchronous boundary here and route async pressure through the indexed [async input](../../raw/async.md) |
| Exact multiline behavior of future `[]` and other constructs must remain coherent with open-delimiter and comma continuation rules | Preserve as a source-structure follow-up; `006` decides only the concrete invocation/result-list consequences |
| Hidden `mutator` invocation, runtime value-polymorphic dispatch, variadics, partial application, composition, chaining, and split/combine transforms are distinct mechanisms | Preserve their separation from ordinary fixed-arity calls and route each through its later function, flow, operator, or variadic owner |
| Reflection should eventually expose visible labels, defaults, result discardability, adapted contracts, and declared/generated/unavailable distinctions | Preserve as future function/reflection/tooling pressure; do not let reflection needs redefine current invocation syntax |
| Formal grammar, conformance details, ABI, lowering, and compiler representation remain later specification or implementation concerns | Preserve programmer-visible constraints here; do not add implementation mappings to language teaching |

Raw-owner references in this ledger are proposed dispositions, not authorization
to edit those files now. During authorized promotion or separately authorized
raw-input maintenance, update an existing raw owner only when the dry run
confirms that it must preserve the new consequence.

#### Defensible deferrals

The aligned synchronous model preserves these later concerns without allowing
them to invalidate current conclusions:

- The detailed acyclic per-slot precedence table for types, qualifications,
  indirection, copy, move, reference, pointer, and `last` remains later focused
  work. The partial-order dominance structure is established now.
- Complete closure layout, capture syntax, reassignment, recursive function-value
  representation, and raw function pointers remain function-value work.
- Generic inference, concepts, specialization, and generic result deduction must
  eventually produce a concrete prototype before ordinary binding completes.
- Variadics, partial application, composition, chaining, split/combine
  operations, and arbitrary result spreading beyond the aligned fixed result
  mapper remain their focused work.
- Complete moved-from states, ownership transfer, destruction responsibility,
  reference origin, borrow extension, place tracking, returned aliases, and
  temporary destruction order remain pointer/lifetime work.
- Async calls, cancellation, suspension, executors, and concurrency require a
  completion model broader than synchronous return.
- Runtime value-polymorphic predicates, hidden `mutator` access, module/import
  lookup priority, and arbitrary operator candidate domains remain their
  respective owners.
- Explicit promotion of several results into one anonymous or structural value is
  desirable future structural-type work preserved through the indexed
  [structural-typing input](../../raw/structural-typing.md). Ordinary mapping and
  construction must keep that operation visible rather than performing it
  implicitly.
- Language-specific onboarding and migration primers remain future comparison
  work preserved through the indexed
  [language-similarity input](../../raw/similarity.md). Function documentation
  teaches Zax directly.
- Anonymous/private parameter and result labels, label aliases, canonical
  anonymous typed declaration formatting, and reflection metadata remain later
  function syntax, compatibility, and tooling refinements.
- Exact multiline behavior for future delimiters remains source-structure work
  constrained by the grouping, comma-list, indentation, and redundant
  continuation findings established here.
- Formal grammar, diagnostic identifiers, ABI, lowering, compiler data
  structures, and implementation algorithms remain future specification or
  implementation concerns.

#### Review readiness

No known unresolved contradiction remains within the bounded `006` concern.
The detailed transfer-precedence table is an explicit deferral rather than a
hidden dependency. These aligned findings are ready for language-maintainer
review in the working record.

The authorized pre-promotion documentation fit dry run is recorded in the
following section.

## Dispositions and promotion dry run

| Field | Value |
| --- | --- |
| Date | 2026-08-24 |
| Status | Completed read-only documentation fit dry run |
| Result | Successful structure fit; no language-design blocker found |
| Promotion State | Subsequently aligned, authorized, and applied; see promotion outcome below |

This dry run did not promote findings or edit any owner, legacy, router, raw, or
archive file. It tests whether every aligned `006` finding can reach a coherent
lasting owner without duplicate authority, missing documentation structure, or
lost future input.

### Dry-run verdict

The dry run succeeded.

The current repository can absorb the aligned `006` findings with:

- one new focused current owner at `language/function-invocation.md`;
- targeted updates to existing declaration, qualifier, construction, source
  structure, terminology, and documentation-practice owners;
- value-based cleanup and current-owner routing in affected legacy pages;
- updates to existing indexed raw inputs for future lifetime, safety, analysis,
  async, and structural work; and
- retirement of the fully consumed raw function-invocation input.

No new directory family, language index, formal specification area, compatibility
stub, or implementation documentation is needed.

No aligned language behavior needs further refinement before promotion.
The detailed transfer-precedence table remains an explicit future deferral and
does not prevent the conceptual documentation from teaching the established
partial-order model.

Promotion still requires ordinary maintainer alignment on the proposed owner
path, value-based legacy cleanup, raw-input updates, and exact multi-file change
set below. Those are promotion decisions, not evidence that the dry run failed.

### Proposed repository structure

Retain the current repository layout and add one file:

```text
language/
    construction-and-destruction.md
    declarations-and-bindings.md
    function-invocation.md          # new current concept owner
    qualifiers.md
    source-structure.md
    terms.md
    vision.md
```

`language/function-invocation.md` is preferable to `language/functions.md`
because the aligned work does not own complete captures, closure representation,
function reassignment, variadics, async calls, value-polymorphic dispatch,
`mutator` access, or every function-like mechanism. The narrower name states the
actual accepted scope and leaves the root [legacy functions page](../../../functions.md)
available for continuing future evidence.

No `language/README.md` is needed. The public [website entry point](../../../index.md)
already routes current conceptual design and can add the new owner directly.

No formal specification file should be created. The current owner must preserve
all material programmer-visible detail in a teachable layered form so a future
specification can formalize rather than rediscover the design.

### New function-invocation owner

The proposed `language/function-invocation.md` would use current-owner metadata:

| Field | Proposed value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax calls |
| Applies To | Programmer-facing synchronous function invocation, argument/default binding, results, and callable selection; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | Ordinary call surface; visible callable contracts; positional/named/omitted/type-default inputs; call-entry order; result slots and completion; multiple-result expression/mapping modes; result routing; fixed-arity overload viability and partial-order preference; adapted compatible prototypes; synchronous call completion; invocation diagnostics, costs, and formatting |
| Does Not Own | Complete function declaration/capture/reassignment; complete copy/move/`last` precedence; pointer/reference provenance; generics; variadics; hidden `mutator` access; runtime value polymorphism; arbitrary operator lookup domains; async; ABI; formal grammar; compiler implementation |

Its teaching order should be:

1. ordinary zero- and one-result calls;
2. the visible callable prototype and receiver operand;
3. positional and named arguments;
4. caller evaluation and immediate binding;
5. omission, declared defaults, and explicit type-default expressions;
6. result slots, direct return, preconstruction, and completion;
7. multiple-result capture and acknowledgement;
8. expression mode versus result-mapping mode;
9. result-routing groups, labels, cursors, and forwarding;
10. overload viability, partial-order preference, and explicit disambiguation;
11. resolved function values and adapted visible contracts;
12. temporary lifetime and synchronous completion;
13. advanced behavior and reference details;
14. diagnostics, costs, source stability, and formatting; and
15. explicit boundaries and deferrals.

Rules and advanced behavior remain in the owner. Historical debate, rejected
syntax, and discovery chronology remain in this work record.

### Lasting ownership map

| Aligned finding | Lasting owner and local responsibility |
| --- | --- |
| Ordinary call syntax and invocation phases | New `language/function-invocation.md` |
| Positional, named, explicit positional, omitted, and type-default inputs | New function owner |
| Parameter and result labels as visible call-contract elements | New function owner; declaration-facing syntax summarized in [declarations and bindings](../../../language/declarations-and-bindings.md) |
| Caller source-entry order and selected-callee default order | New function owner |
| Declaration expressions used as inputs and mixed result captures | Declaration owner defines declaration behavior; function owner defines call/result mapping |
| Visible defaults and result `#` metadata on adapted prototypes | New function owner |
| Directly compatible prototype binding without an executable adapter | New function owner |
| Result slots, opt-in initialization, direct return, bare return, and fallthrough | New function owner; declaration owner records the specialized result declaration context |
| Zero, one, and multiple-result shapes | New function owner |
| Bare result sequences, expression mode, contextual `=` mapping, routing groups, and cursor behavior | New function owner |
| Result labels, same-name shorthand, remapping, discard, omission, and existing destinations | New function owner; declaration owner owns introduced/existing binding distinctions |
| Narrow complete-declaration result selection and API discoverability | New function owner |
| Fixed-arity viability, partial-order dominance, fewer-default preference, ambiguity, and unavailable best candidates | New function owner |
| Detailed type/qualifier/reference/pointer/copy/move/`last` relations | [Qualifiers](../../../language/qualifiers.md) retains qualification truth; detailed precedence remains future function/lifetime work |
| Receiver-operand qualification and temporary qualification | Qualifier owner defines axes; new function owner applies them to invocation |
| Construction packet's revised ordinary positional input and `.member` distinction | [Construction, replacement, and destruction](../../../language/construction-and-destruction.md) |
| Shared construction-call defaults, evaluation, binding, and overload behavior | New function owner owns the general rule; construction owner shows packet-specific use and member lifecycle order |
| Comma-list continuation, grouping-expression boundary, redundant `\`, and indentation intent | [Source structure](../../../language/source-structure.md) |
| Cross-cutting callable/result terminology | [Language-design terms](../../../language/terms.md), only for terms used across several owners |
| Layered teachability and preservation of material detail | [Documentation architecture](../../documentation.md) |
| Immediate temporary survival and post-call escape boundary | New function owner states the synchronous boundary; future detail goes to indexed lifetime input |
| Unsafe proof/override and mandatory safe-subset guarantees | Existing indexed analysis-control and safety inputs |
| Async suspension and delayed completion | Existing indexed async input |
| Explicit promotion of result sequences into one structural value | Existing indexed structural-typing input |
| Language-specific migration and similarity primers | Existing indexed similarity-guide input; no function-owner comparison guide |
| ABI, lowering, parser algorithms, compiler representation, and conformance | Future specification or implementation owners, not this repository's current language teaching |

### Focused reading after promotion

The ordinary public reading path would be:

1. [Website entry point](../../../index.md);
2. `language/function-invocation.md`;
3. a linked prerequisite only when the reader needs its concern:
   - [source structure](../../../language/source-structure.md) for multiline and
     intent layout;
   - [declarations and bindings](../../../language/declarations-and-bindings.md) for
     declaration expressions, introduced names, and assignment;
   - [qualifiers](../../../language/qualifiers.md) for value/access/place truth; or
   - [construction](../../../language/construction-and-destruction.md) for packets,
     stored members, and lifecycle operations.

The function owner should teach ordinary use without requiring readers to open
all four prerequisites first. It links at the point where a boundary becomes
material.

The construction owner links back to the function owner for shared parameter
defaults, candidate selection, explicit input order, result routing, and call
completion. It retains local packet examples so construction readers do not need
to reconstruct `.member` behavior from the general call document.

Indexed raw inputs, the active work file, and archived work remain outside the
ordinary public language reading path.

### Current owners affected by promotion

#### `language/function-invocation.md` — create

Create the teachable current owner described above. This is the only new file.

#### [Zax source structure](../../../language/source-structure.md) — modify

- Add comma-list continuation as a recognized statement-level continuation
  reason.
- State that a comma continues only an already established list.
- Define one continuation reason per physical newline.
- Reject redundant `\` where an open delimiter or comma already continues.
- Permit a hanging-indented continuation-only `\` line after comma continuation.
- Preserve blank/comment-only break behavior and mandatory indentation intent.
- State that grouping parentheses require one expression; call argument
  delimiters and construction packets may accept their own mapping grammars.
- Add corresponding diagnostics, formatting constraints, ownership links, and
  boundary wording.

The source owner can absorb these rules without changing its basic
grammar-versus-layout model.

#### [Zax declarations and bindings](../../../language/declarations-and-bindings.md) — modify

- Add the new function owner to the ownership boundary.
- Keep default function-value initialization here but route invocation of
  default-`Nothing` values to the function owner.
- Replace the current default-parameter deferral with a declaration-facing
  summary and link to the function owner.
- Replace the current positional interpretation of `first:, second:` with
  source-result label semantics, explicit remapping, complete typed capture,
  mixed new/existing destinations, and a link to the function owner.
- Explain result slots as a specialized declaration context whose lifetime may
  begin through return, explicit `+++`, or an opted-in initializer.
- Preserve ordinary declaration-versus-assignment rules within mixed captures.
- Update declaration diagnostics and maturity boundaries.

These changes resolve a current direct conflict rather than creating duplicate
function semantics.

#### [Zax qualifiers](../../../language/qualifiers.md) — modify

- Add the function owner to the ownership boundary.
- Retain the authoritative per-layer parameter, result, reference, pointer,
  receiver, and temporary qualification rules.
- Link ordinary call viability, result routing, adapted prototypes, and
  preference application to the function owner.
- Clarify that adapted visible prototypes must remain qualification-compatible
  with ordered implementation slots.
- Preserve incomplete copy/move/`last` ranking as an explicit future boundary.
- Update diagnostics and maturity wording where invocation now has a current
  owner.

#### [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md) — modify

- Add the new function owner to the ownership boundary.
- Revise construction packets so ordinary positional inputs use bare expressions;
  leading `:` remains explicit positional intent.
- Add empty positional/named omission and explicit type-default forms shared with
  ordinary calls.
- Preserve `.member = expression` as the construction-only stored-member entry.
- Update cursor rules so explicit omission maps a slot but does not evaluate in
  the explicit-input phase; `.member` still invalidates the constructor cursor.
- Make general default evaluation, explicit input order, immediate binding,
  callable selection, result routing, and call completion links to the function
  owner while retaining packet-specific examples.
- Distinguish input evaluation from member lifecycle-plan order.
- Add multiple-result forwarding into constructor parameters without implying
  structural packing.
- Update replacement-result, diagnostics, costs, formatting, and maturity
  sections.

#### [Zax language-design terms](../../../language/terms.md) — modify

Add concise cross-cutting terms only where several current owners use them:

- callable;
- parameter slot;
- result slot;
- result shape;
- result-routing group; and
- call completion.

Each term links to the function owner for behavior. Do not turn the terms page
into another invocation definition.

#### [Documentation architecture](../../documentation.md) — modify

Extend human-developer-facing depth guidance to state:

- current owners begin with a mental model and common examples;
- materially important rules, diagnostics, costs, corner cases, and advanced
  behavior remain in current documentation;
- advanced or reference-detail sections may preserve necessary depth without
  interrupting the ordinary learning path;
- working history and rejected alternatives need not enter teaching;
- current conceptual documentation should leave future specification work with
  behavior to formalize rather than discoveries to repeat; and
- language-specific comparison guides remain separate reader routes.

This is general project guidance learned through `006`, not language semantics.

#### [Language vision](../../../language/vision.md) — retain unchanged

The current readability, explicit-cost, and source-stability principles already
support the aligned invocation design. Adding invocation mechanics or another
abstract philosophy statement would overload the foundational owner.

### Public router change

#### [Website entry point](../../../index.md) — modify

- Add `language/function-invocation.md` to **Start here** after declarations and
  qualifiers or after construction according to the final teaching route.
- Add it to **Current conceptual design**.
- Keep the root `functions.md` page under **Legacy language-design notes**.
- Describe the new owner narrowly enough that readers do not assume complete
  captures, generics, variadics, async, or function representation are accepted.

The root [README](../../../README.md) remains unchanged because it already routes
language readers through the website entry point.

The [project router](../../README.md) remains unchanged during promotion because it
must continue to identify active work `006` until separately authorized closure.

### Legacy material disposition

Legacy pages remain public evidence only where they retain future value. Fully
consumed or rejected sections should not continue teaching a competing model.

#### [Legacy functions](../../../functions.md) — modify and prune by value

Update the preamble to identify `language/function-invocation.md` as the current
owner for ordinary calls, argument/default mapping, results, routing, fixed-arity
selection, adapted visible contracts, and synchronous completion.

Remove or replace fully consumed legacy sections for:

- basic ordinary invocation;
- multiple fixed results and arguments;
- ordinary fixed-arity polymorphism;
- default arguments;
- ordinary member-function invocation and readonly receiver-call behavior; and
- directly compatible visible-prototype reassignment now covered by the current
  owner.

Retain clearly labeled future evidence for:

- captures and closure representation;
- function reassignment and raw function pointers;
- function composition, chaining, and partial application;
- `mutator` behavior;
- immediate anonymous-function invocation where function construction remains
  unsettled;
- `once`;
- complete copy/move/`last` transfer behavior;
- variadics; and
- split/combine and structural argument/result transforms.

The page remains a legacy future-input page rather than a competing current
owner.

#### [Legacy constructors and destructors](../../../ctor-dtor.md) — modify and prune

- Add the new function owner to the current-routing preamble for shared call
  behavior.
- Remove the legacy named/mixed construction-input sections whose punctuation and
  fallback behavior are superseded by the revised current construction packet.
- Retain future allocation, global, `once`, move/copy/`last`, and other explicitly
  unreviewed evidence already named by the page.

#### [Legacy discard operator](../../../discard.md) — modify and prune

- Add a current-owner preamble.
- Remove or replace legacy function-result and input-placeholder sections now
  superseded by result acknowledgement, omission, and explicit type-default
  behavior.
- Retain unrelated local/type/memory discard evidence for future review.

#### [Legacy flow control](../../../flow-control.md) — modify routing note only

Add the new function owner to the current-routing preamble for calls, return
completion, result labels, and capture used by flow examples. Do not redesign
flow-control behavior during this promotion.

#### [Legacy operator overloading](../../../operator.md) — modify

- Link the new function owner for shared callable viability, partial-order
  dominance, result-context limits, and ambiguity.
- Explicitly mark source/import-order operator fallback as superseded and remove
  wording that selects the first imported module.
- Retain operator lookup domains, word/literal operators, and operator-specific
  priority as future operator input.

#### [Legacy pointers and references](../../../pointers.md) — modify routing note

Link the new function owner for immediate call-boundary reference, pointer, move,
`last`, temporary, and result behavior. Retain complete ownership, origin,
provenance, and transfer precedence as future pointer/lifetime evidence.

#### [Legacy Nothing](../../../nothing.md) — modify routing note

Route invocation of default-`Nothing` function values to the new function owner
while retaining unreviewed general `Nothing` representation and instance behavior.

#### [Legacy variadics](../../../variadic.md) — add boundary preamble

State that current fixed-arity invocation and overload behavior is owned by the
new function owner. Keep variadic expansion, enumeration, and selection as
legacy future input that must fit that base model.

#### [Legacy meta-functions](../../../meta-functions.md) — add boundary preamble

State that ordinary expected-result context and callable preference are owned by
the new function owner. Keep omitted meta types and compile-time specialization
as legacy future input; they may not broaden ordinary result inference silently.

#### [Legacy except handling](../../../except.md) — add boundary preamble

State that ordinary result slots, labels, routing, conversion boundaries, and
completion are owned by the new function owner. Keep `except`/`catch`
short-circuit behavior as unreviewed future input and do not let its legacy
constructor-graph matching redefine ordinary result selection.

#### [Legacy warnings and errors](../../../warnings-errors.md) — modify routing note

Link current invocation validity and diagnostic categories to the new function
owner while preserving the registry and exact identifiers as legacy future
diagnostics input.

No other root legacy page needs a promotion edit. Calls appearing incidentally
inside unrelated legacy examples remain covered by the public index's legacy
status and do not become current merely because invocation now has an owner.

### Raw-input disposition

#### Raw function invocation — retire

Every retained construction-derived constraint is now:

- promoted into the new function owner or current construction owner;
- preserved in the active aligned record;
- routed into lifetime/safety/analysis/async/structural raw input; or
- explicitly deferred with future pressure.

Delete this file during promotion and remove its row from the
[raw-input index](../../raw/README.md). Keeping it would create a stale competing
input after its retirement criteria are satisfied.

#### [Raw feature catalog](../../raw/feature-catalog.md) — modify

Add recorded dispositions for:

- optional function arguments;
- multiple function results;
- fixed-arity function polymorphism;
- member-function invocation; and
- result-discard acknowledgement.

Keep captures, variadics, value polymorphism, async calling, anonymous types, and
other unreviewed catalog entries unresolved.

#### [Raw lifetime strategies](../../raw/lifetimes.md) — modify

Add invocation-derived pressure covering:

- argument and result temporaries surviving synchronous nested calls;
- returned references not automatically inheriting that extension;
- exact source authority transfer timing;
- result-to-parameter reference binding;
- moved-from source states; and
- async calls requiring a broader lifetime boundary.

#### [Raw analysis controls](../../raw/analysis-controls.md) — modify

Add invocation-derived future proof/override pressure for:

- result completion on every normal path;
- at-most-once result construction;
- use after move or `last`;
- returned-reference escape;
- source/destination result consumption; and
- diagnostics explaining which contract proof failed.

Do not invent final unsafe-control syntax or identifiers.

#### [Raw safety boundaries](../../raw/safety.md) — modify

Add invocation-derived safety questions for:

- use before result construction;
- incomplete or duplicate result completion;
- dangling references returned from argument/result temporaries;
- post-transfer use;
- reentrant observation between explicit bindings; and
- which cases the future safe subset guarantees are rejected.

#### [Raw async](../../raw/async.md) — modify

Add ordinary-call suspension pressure:

- when an initiating call is considered complete;
- where parameters, defaults, temporaries, and result destinations live;
- cancellation after some inputs or results are complete;
- callbacks after the initiating expression returns; and
- how async calls expose their runtime cost.

#### [Raw structural typing](../../raw/structural-typing.md) — modify

Add the explicit future pressure for promoting a multiple-result sequence into
one anonymous structural value. Preserve the distinction from:

- ordinary result routing;
- constructor-parameter forwarding; and
- `.member` construction inputs.

#### [Raw similarity guide](../../raw/similarity.md) — retain unchanged

It already preserves future language-specific primers, bounded comparison, and
the rule that concept owners teach Zax itself.

No new raw file is needed.

### Exact proposed promotion change set

The dry run proposed exactly these changes. They were subsequently aligned,
authorized, and applied as recorded in the promotion outcome below:

#### Create

1. `language/function-invocation.md`

#### Modify current owners and routers

2. [Website entry point](../../../index.md)
3. [Documentation architecture](../../documentation.md)
4. [Zax source structure](../../../language/source-structure.md)
5. [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
6. [Zax qualifiers](../../../language/qualifiers.md)
7. [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
8. [Zax language-design terms](../../../language/terms.md)

#### Modify or prune affected legacy pages

9. [Legacy functions](../../../functions.md)
10. [Legacy constructors and destructors](../../../ctor-dtor.md)
11. [Legacy discard operator](../../../discard.md)
12. [Legacy flow control](../../../flow-control.md)
13. [Legacy operator overloading](../../../operator.md)
14. [Legacy pointers and references](../../../pointers.md)
15. [Legacy Nothing](../../../nothing.md)
16. [Legacy variadics](../../../variadic.md)
17. [Legacy meta-functions](../../../meta-functions.md)
18. [Legacy except handling](../../../except.md)
19. [Legacy warnings and errors](../../../warnings-errors.md)

#### Modify indexed raw material

20. [Raw-input index](../../raw/README.md)
21. [Raw feature catalog](../../raw/feature-catalog.md)
22. [Raw lifetime strategies](../../raw/lifetimes.md)
23. [Raw analysis controls](../../raw/analysis-controls.md)
24. [Raw safety boundaries](../../raw/safety.md)
25. [Raw async](../../raw/async.md)
26. [Raw structural typing](../../raw/structural-typing.md)

#### Delete consumed raw material

27. `project/raw/function-invocation.md`

#### Explicitly unchanged during promotion

- [Repository entry point](../../../README.md)
- [Project router and active-work index](../../README.md)
- [Language vision](../../../language/vision.md)
- [Raw similarity guide](../../raw/similarity.md)
- handoff and rehydration prompt sources;
- archive contents;
- unrelated legacy topic pages; and
- all implementation repositories or files.

Staging, committing, pushing, closure, and archival remain separately authorized
actions. Owner edits and the raw-file retirement in this exact set were
subsequently authorized.

### Findings requiring alignment before promotion

The dry run found no missing language decision that blocks promotion.

The following documentation decisions still require maintainer alignment as the
normal next step:

1. **New owner path and scope:** accept `language/function-invocation.md` with the
   ownership boundary and teaching order above.
2. **Layered detail:** accept that the current owner contains common teaching,
   advanced behavior, diagnostics, costs, and reference details rather than
   splitting a premature specification.
3. **Legacy cleanup:** accept value-based pruning of fully consumed function,
   construction-input, and result-discard sections instead of leaving stale
   competing examples behind broad legacy notices.
4. **Raw maintenance:** accept retirement of raw function-invocation input and
   the targeted additions to existing lifetime, analysis, safety, async,
   structural, and feature-catalog raw owners.
5. **General documentation guidance:** accept the small documentation-architecture
   update for layered teachability and preservation of material detail.
6. **Exact promotion file set:** accept the 27-path create/modify/delete set above
   as one coherent promotion boundary.

If any of these choices is rejected, refine the structure or file set before
promotion. None currently requires reopening the aligned invocation semantics.

### Success assessment

The dry run is a success:

- every aligned finding has one current owner or explicit future disposition;
- the proposed new owner can absorb the material coherently;
- existing owners need bounded changes rather than structural redesign;
- current and raw reading paths remain distinguishable;
- no current owner would depend on the active or archived work record;
- no duplicate authority is required;
- no useful cross-scope consequence is orphaned;
- no formal specification or implementation mapping is introduced;
- legacy material has a value-based retain/prune plan;
- raw function-invocation input can retire without losing evidence; and
- the final promotion can remain teachable while preserving detailed behavior
  needed by advanced programmers and future specification work.

### Promotion outcome

| Field | Value |
| --- | --- |
| Date | 2026-08-24 |
| Status | Authorized promotion applied |
| Changed promotion paths | Exact 27-path create/modify/delete set proposed above |
| New current owner | `language/function-invocation.md` |
| Raw retirement | `project/raw/function-invocation.md` deleted after all retained material received a destination |

Promotion:

- created the teachable current function-invocation owner;
- updated every affected current owner and public router;
- preserved material rules, diagnostics, costs, advanced behavior, and
  future-specification pressure without introducing a formal specification;
- pruned fully consumed competing legacy function, construction-input, and
  discard sections while retaining future evidence;
- routed remaining legacy pages to current invocation behavior;
- updated indexed lifetime, analysis, safety, async, structural, and feature
  inputs;
- retired the consumed raw function-invocation input and its index entry; and
- left the language vision, repository router, active-work index, operating
  prompts, archive, and unrelated legacy pages unchanged.

Validation confirmed:

- the actual promotion paths match the authorized 27-path set;
- non-archive Markdown links and anchors introduced or affected by promotion
  resolve;
- changed Markdown code fences are balanced;
- staged and unstaged diffs contain no whitespace errors;
- no live Markdown link targets the retired raw input;
- no duplicate current invocation owner is required; and
- a skeptical consistency review found no promotion blocker.

At this promotion checkpoint, the active work item remained open for maintainer
review. Closure, archival, work item `007`, staging, committing, and pushing
were not authorized by that promotion.

## Raw philosophy statement inventory

| Field | Value |
| --- | --- |
| Date | 2026-08-24 |
| Status | Superseded classification retained as raw inventory |
| Scope | Every raw philosophy or design-guidance statement enumerated after promotion |
| Result | All 43 statements preserved; weighting and owner recommendation superseded by the revised principle-catalog audit below |

This audit asks a different question from the documentation fit dry run:

> Did promotion preserve each simple idea with the weight it had in discussion,
> rather than merely preserve a local rule that follows from it?

The answer is not yet fully yes. The new function owner teaches the invocation
model well, and project documentation preserves most teaching and disposition
guidance. Several broader principles, however, were narrowed to invocation or
left implicit. They need explicit owner decisions before `006` closes.

The tables in this section preserve the first statement-by-statement inventory.
They incorrectly flatten foundational principles, reusable design/use insights,
local semantic rules, project-design guidance, and documentation guidance into
one list. Use the revised principle-catalog audit below for current weighting and
owner recommendations.

### Audit meanings

| Audit result | Meaning |
| --- | --- |
| Full | The wider documentation preserves the statement at its intended weight |
| Narrowed | A local application was promoted, but the broader statement was not |
| Implicit | Current behavior supports the idea, but no owner states it plainly |
| Working-only | The idea remains only in `006` or promotion reasoning |
| Absent | No current or indexed raw owner preserves the idea |

### Language-wide principles

These statements potentially guide several language concepts rather than only
ordinary function calls.

| # | Raw statement | Weight | Current capture and audit | Recommendation |
| --- | --- | --- | --- | --- |
| 1 | Zax can provide flexible syntax and matching when the programmer's intended operation remains clear. | Language-wide design principle | **Narrowed.** [Function invocation](../../../language/function-invocation.md#mental-model) says flexible *call forms* are available when call intent is clear. | Add the original broad statement to [language vision](../../../language/vision.md) as a short design-principle bullet. Keep the call-specific application locally. |
| 2 | Be explicit about intent. | Language-wide design principle | **Narrowed.** Labels, declaration spacing, transfer markers, and mapping make invocation intent explicit, but no broad owner states the principle. | Add a separate concise vision statement. Do not fold it into a catalogue of invocation mechanisms. |
| 3 | Stop when intent becomes ambiguous; require the programmer to disambiguate. | Language-wide matching and source principle | **Narrowed.** Callable ambiguity is current, but the wider principle is unstated. | Add a separate vision statement that Zax requires explicit disambiguation instead of guessing when several meanings remain plausible. |
| 7 | Similar mechanisms should use similar syntax; different mechanisms should remain visibly distinct. | Language-wide consistency principle | **Implicit.** Calls and construction now share inputs, while `.member`, expression mode, and mapping remain distinct. | Add a concise statement to the vision's familiarity/readability principle. Keep concrete syntax in local owners. |
| 8 | Visible source order should remain trustworthy. | Language-wide predictability principle | **Narrowed.** Invocation and construction define observable order, but the wider principle is not stated. | Add a concise vision statement that observable ordering follows visible source rules and hidden work must have a documented position. |
| 20 | Pay only for behavior that is used. | Foundational cost principle | **Superseded capture.** The broad principle moved from the vision into [Zax language principles](../../../language/principles.md#costs-follow-chosen-behavior-and-remain-discoverable); [function invocation](../../../language/function-invocation.md#costs) applies it to contracts and defaults. | No further change required. |
| 22 | Optimization may remove work only when it preserves the programmer-visible operation. | Language-wide semantic-preservation principle | **Narrowed.** Invocation states the local rule; no broad current owner states it. | Add a short vision-level statement. Future formal semantics may define exact equivalence, but the conceptual direction should not remain call-only. |
| 23 | Consequential costs and risks should remain discoverable. | Foundational cost/documentation principle | **Full.** The vision owns visible cost; function, construction, qualifier, and documentation owners expose local consequences. | No change required. |

### Invocation and source-domain principles

These statements have deliberately bounded owners. They do not all belong in the
vision merely because they are important.

| # | Raw statement | Weight | Current capture and audit | Recommendation |
| --- | --- | --- | --- | --- |
| 4 | Choose a best overload only when it is obviously better. | Callable-selection principle | **Full.** [Partial-order preference](../../../language/function-invocation.md#partial-order-preference) defines dominance and incomparability. | Retain in the function owner. |
| 5 | Do not use source, declaration, import, or generation order to guess among equal matches. | Callable/operator selection principle | **Full.** Function and legacy operator routing reject order-based ties. | Retain in function and future operator owners. |
| 6 | Parsing should not depend on the current overload set. | Call/source parsing principle | **Full.** [Whitespace and intent](../../../language/function-invocation.md#whitespace-and-intent) states that overload changes cannot reinterpret the parse. | Retain locally; source structure may link if later parsing work needs the general boundary. |
| 9 | Omission and explicit type-default initialization are different operations. | Invocation principle | **Full.** [Omitted inputs and defaults](../../../language/function-invocation.md#omitted-inputs-and-defaults) teaches both forms and order. | No change required. |
| 10 | Inputs may be flexible, but every normal result is complete. | Invocation/result mental model | **Implicit.** The rules are complete, but the simple contrast is not stated. | Add one short mental-model paragraph to the function owner before detailed result rules. |
| 11 | Multiple results are an ordered result sequence, not one hidden value. | Result-model principle | **Full.** Result slots, expression/mapping modes, and construction forwarding preserve it. | No change required. |
| 12 | Packing several results into one value must be explicit. | Result/structural boundary | **Full.** Function and construction owners preserve the boundary; [raw structural input](../../raw/structural-typing.md#promoting-multiple-results-into-one-structural-value) owns future pressure. | No change required. |
| 13 | Parentheses are not cosmetic at a multiple-result boundary. | Invocation/source principle | **Full.** Function and source-structure owners teach expression mode. | No change required. |
| 14 | A comma and `\` communicate different continuation intent. | Source-structure principle | **Full.** [Comma-list continuation](../../../language/source-structure.md#comma-list-continuation) and invocation return examples own it. | No change required. |
| 15 | Do not write defensive continuation punctuation just in case. | Source-intent principle | **Full.** Source structure rejects redundant continuation. | No change required. |
| 16 | Explicit result types can improve API naming and discoverability. | Invocation/API-design principle | **Full.** Narrow result selection preserves the `parseValue` rationale. | No change required. |
| 17 | Result-directed selection must remain narrow and must not search arbitrary conversion graphs. | Invocation-selection principle | **Full.** [Narrow expected-result selection](../../../language/function-invocation.md#narrow-expected-result-selection) owns it. | No change required. |
| 18 | A public callable contract can improve labels, defaults, and acknowledgement without rewriting the implementation. | Callable-contract principle | **Full.** [Compatible visible prototypes](../../../language/function-invocation.md#compatible-visible-prototypes) owns it. | No change required. |
| 19 | Direct compatibility means no executable adaptation is needed. | Callable-compatibility principle | **Full.** The function owner requires a wrapper or lambda when transformation is needed. | No change required. |
| 21 | Results should not pay an automatic default-construction penalty. | Result/cost principle | **Full.** Result slots begin unconstructed and opt into initialization. | No change required. |

### Language-design process principles

These govern how language concepts are evaluated and preserved. Their likely
owner is project guidance rather than a language concept page.

| # | Raw statement | Weight | Current capture and audit | Recommendation |
| --- | --- | --- | --- | --- |
| 24 | Implementation feasibility is legitimate design pressure even when implementation is not owned here. | Project design-method principle | **Working-only.** `006` records it, and promoted syntax reflects it, but wider project guidance does not state it. | Add a concise rule to [documentation architecture](../../documentation.md): implementation feasibility may pressure conceptual design, while implementation mappings remain outside language owners. |
| 25 | Do not put compiler implementation mappings into language teaching. | Documentation/ownership principle | **Full.** Documentation architecture and current-owner boundaries preserve it. | No change required. |
| 26 | Follow necessary consequences wherever they lead; that is not improper scope expansion. | Numbered-work principle | **Full.** [Focused reading](../../documentation.md#focused-reading) and consequence guidance require this. | No change required. |
| 27 | Do not lose a finding because its lasting owner lies outside the active work item. | Numbered-work/disposition principle | **Full.** Documentation architecture requires explicit disposition and the `006` ledger demonstrates it. | No change required. |
| 28 | A deferral must preserve why it matters and what pressure reopens it. | Numbered-work principle | **Full.** [Consequences and deferrals](../../documentation.md#consequences-and-deferrals) owns it. | No change required. |
| 29 | A contradiction cannot be hidden through deferral. | Numbered-work correctness principle | **Full.** Documentation architecture states this directly. | No change required. |

### Documentation and teaching principles

| # | Raw statement | Weight | Current capture and audit | Recommendation |
| --- | --- | --- | --- | --- |
| 30 | Promotion is for teaching the selected model, not reproducing the design meeting. | Documentation principle | **Full.** [Human-developer-facing depth](../../documentation.md#human-developer-facing-depth) separates current teaching from working history. | No change required. |
| 31 | Examples are essential teaching tools. | Documentation principle | **Implicit.** Current owners begin with examples, but project guidance does not state the importance plainly. | Add a short explicit examples rule to documentation architecture. |
| 32 | Introduce concepts in prerequisite order. | Documentation principle | **Implicit.** Focused reading and layered depth exist, but teaching order is not stated directly. | Add a concise rule that examples and sections introduce prerequisites before depending on them. |
| 33 | Assume a competent programmer rather than teaching programming from first principles. | Audience principle | **Implicit.** Documentation says human-first is not introductory or simplified, but it does not positively state the assumed baseline. | Strengthen the existing audience guidance with a bounded competent-programmer baseline while retaining clear explanations of Zax-specific behavior. |
| 34 | Teach Zax directly rather than organizing it around another language. | Documentation principle | **Full.** Documentation architecture states this directly. | No change required. |
| 35 | Compare with another language only when it resolves a concrete misunderstanding. | Documentation principle | **Full.** Documentation architecture states this directly. | No change required. |
| 36 | Language-specific primers may come later as separate reader routes. | Documentation/reader-route principle | **Full.** Documentation architecture and [raw similarity input](../../raw/similarity.md) preserve it. | No change required. |
| 37 | Do not teach rejected bad ideas merely to reject them. | Documentation principle | **Full.** Working history remains in project records; promoted teaching omits rejected alternatives. | No change required. |
| 38 | Teach accepted, likely-to-surprise boundaries with counterexamples. | Documentation principle | **Full.** Documentation architecture states the exception and function/source owners use counterexamples. | No change required. |
| 39 | Simple teaching does not mean omitting important detail. | Documentation principle | **Full.** Material rules, diagnostics, costs, corner cases, and advanced behavior remain current. | No change required. |
| 40 | Layer detail instead of making the whole document dense. | Documentation principle | **Full.** Documentation architecture explicitly permits common, advanced, diagnostic, cost, and reference layers. | No change required. |
| 41 | Future specification work should formalize rather than rediscover accepted behavior. | Documentation/specification principle | **Full.** Documentation architecture states this directly. | No change required. |
| 42 | Working records and owner documents have different purposes. | Authority/lifecycle principle | **Full.** Working records preserve history; current owners teach accepted design. | No change required. |
| 43 | A disposition ledger supports understandable findings; it does not replace them. | Numbered-work/review principle | **Working-only.** `006` states it, but project guidance does not. | Add a concise numbered-work rule to documentation architecture so future dry runs preserve readable findings before routing them. |

### Superseded audit result

Of the 43 raw statements:

- 31 are preserved at their intended weight;
- 6 language-wide principles are narrowed or implicit;
- 1 invocation mental-model statement is underemphasized;
- 1 project design-method principle remains working-only; and
- 4 documentation/review principles are implicit, absent, or working-only.

The twelve statements needing wider-document attention are:

1. flexible syntax and matching when intent is clear;
2. explicit intent as a broad principle;
3. explicit disambiguation instead of guessing;
4. similar mechanisms using similar syntax;
5. trustworthy visible source order;
6. optimization preserving the programmer-visible operation;
7. flexible inputs versus complete results;
8. implementation feasibility as valid design pressure;
9. examples as essential teaching tools;
10. prerequisite teaching order;
11. competent-programmer audience assumptions; and
12. disposition ledgers not replacing understandable findings.

### Superseded owner recommendation

Do not create a new philosophy or principles document yet.

[Language vision](../../../language/vision.md) already explicitly owns Zax design
principles. Creating `language/design-principles.md` now would split that
authority without demonstrated pressure that the vision cannot absorb concise
statements.

Recommended integration:

1. **Language vision:** add six separate short principles without combining them
   into an omnibus paragraph:
   - flexibility when intent is clear;
   - explicit intent;
   - explicit disambiguation instead of guessing;
   - similar mechanisms using similar syntax;
   - trustworthy visible source order; and
   - optimization preserving programmer-visible behavior.
2. **Function invocation:** add one short mental-model statement that inputs may
   be flexibly selected/defaulted while every normal result is complete.
3. **Documentation architecture:** add concise guidance for:
   - implementation feasibility as conceptual design pressure without importing
     implementation mappings;
   - examples as essential teaching tools;
   - prerequisite teaching order;
   - the competent-programmer audience baseline; and
   - disposition ledgers supporting rather than replacing understandable
     findings.
4. **Existing local owners:** retain their concrete applications and examples.
   The wider principles do not replace local rules.

If later work makes the vision's design-principle section unmanageably large or
independently reusable, a separate principles owner can be proposed through a
future documentation fit dry run. Current evidence does not yet justify that
structural split.

### Superseded audit verdict

The language-behavior promotion remains technically coherent, but philosophy
preservation is incomplete. Work item `006` should not close until the maintainer
reviews and aligns the twelve recommended wider-document corrections above.

No wider-document correction was authorized or applied by this initial audit.
Its recommendation was refined and later promoted as recorded below.

## Revised principle-catalog audit

| Field | Value |
| --- | --- |
| Date | 2026-08-24 |
| Status | Current post-promotion audit / recommendation for review |
| Purpose | Recover reusable Zax principles and design/use insights at their intended weight |
| Result | A dedicated human-facing principles catalog is justified; exact semantics and project/documentation guidance retain separate owners |

### What counts as a principle

A principle is not merely a rule that happens to be important.

A Zax principle:

- explains what the language is trying to make possible or preserve;
- can guide more than one feature or future design decision;
- helps programmers predict why Zax behaves as it does;
- remains useful when one local syntax changes; and
- can be stated simply before its individual applications are explained.

A local semantic rule remains with its concept owner. Project cooperation rules
remain in project guidance. Documentation rules remain in documentation
architecture.

### Primary recovered principle: expressiveness serves clear intent

The central principle discovered through `006` is:

> **Zax seeks expressive and flexible ways for programmers to state their intent
> clearly. When the intended operation remains unclear, Zax rejects the source
> rather than guessing.**

This principle is not about function calls specifically. Function labels,
complete result declarations, construction packets, explicit transfer,
structural matching, conversions, generics, and future mechanisms may all apply
it.

It has two inseparable parts:

1. Flexibility and expressiveness are positive goals because they let source say
   what the programmer means.
2. Flexibility stops at the ambiguity boundary; the language asks for clearer
   source instead of choosing a plausible meaning silently.

The promoted function sentence narrows this to flexible *call forms*. That local
application is correct but does not preserve the foundational principle at its
full weight.

### Why a dedicated principles catalog is justified

[Language vision](../../../language/vision.md) currently owns design principles,
but it also owns purpose, audience, goals, advantages, non-goals, inspiration,
and high-level tradeoffs. The principles emerging from focused concept work are
becoming a reusable catalog with a different reader task:

- programmers use it to understand what Zax values and predict unfamiliar
  behavior;
- API authors use it to design interfaces that fit the language;
- language maintainers use it to evaluate future proposals; and
- future work contributes newly discovered, aligned insights back into the
  catalog.

That independent human-facing use justifies proposing:

```text
language/principles.md
```

The document would not compete with the vision:

- the vision would retain Zax's purpose, audience, goals, non-goals, and
  high-level direction and tradeoffs;
- the vision would summarize and link to reusable principles rather than own a
  second full definition of them;
- the principles catalog would own the growing, reusable set of cross-cutting
  design and use principles; and
- concept owners would continue to own exact behavior and demonstrate local
  applications.

The current vision already contains eleven `Design principles` subsections. A
supplemental dry run must disposition each one:

- **stay** when it primarily establishes foundational direction, feature
  commitment, or a high-level tradeoff;
- **migrate** when it is a reusable principle whose full explanation belongs in
  the catalog; or
- **summarize and link** when the vision needs the principle for orientation but
  the catalog owns its complete statement.

No principle may retain competing full definitions in both files.

This is not analogous to a glossary semantically. The useful analogy to
[language-design terms](../../../language/terms.md) is discoverability: one
maintained catalog gives future work a known place to find and contribute
cross-cutting insights.

### Proposed principles-document boundary

| Field | Proposed value |
| --- | --- |
| Status | Current conceptual principles |
| Audience | Human developers using, evaluating, teaching, or designing Zax |
| Applies To | Cross-cutting principles that guide Zax source, APIs, and future language decisions |
| Implementation State | Not applicable |
| Owns | The human-facing catalog and full definitions of accepted reusable Zax principles and design/use insights |
| Does Not Own | Exact feature semantics, project cooperation rules, documentation process, formal conformance, compiler implementation, or unreviewed aphorisms |

Each catalog entry should contain:

1. a short memorable name;
2. the principle in one or two plain sentences;
3. why it matters to a programmer;
4. a small number of representative applications;
5. an important tension or limit when one exists; and
6. links to current concept owners for exact behavior.

The catalog should not duplicate detailed rules or become a list of slogans.
New entries require discussion, alignment, and authorized promotion like other
accepted conceptual design.

### Candidate catalog principles recovered through `006`

#### 1. Expressiveness serves clear intent

> Zax seeks expressive and flexible ways for programmers to state their intent
> clearly. When the intended operation remains unclear, Zax rejects the source
> rather than guessing.

Recovered from raw statements `1`, `2`, and `3`. This is foundational and
currently **narrowed** to invocation.

Representative applications:

- labels and explicit positional forms;
- complete declarations selecting a result family;
- partial-order ambiguity rather than weighted guessing;
- explicit conversion or transfer boundaries; and
- future structural or generic matching.

#### 2. Similar meaning should look similar; important differences should remain visible

> Mechanisms that perform the same conceptual operation should share readable
> forms. A meaningful semantic difference should remain visible in source.

Recovered from raw statement `7`. This is a cross-cutting usability principle
currently **implicit**.

Representative applications:

- ordinary calls and construction share input forms;
- `.member = expression` remains visibly construction-specific;
- omission differs visibly from explicit type-default initialization; and
- one expression differs visibly from a multiple-result mapping.

#### 3. Visible order should be trustworthy

> Observable work should follow an order programmers can see and learn. Hidden
> work must have one documented position rather than appearing between visible
> operations unexpectedly.

Recovered from raw statement `8`. This is a language-wide predictability
principle currently **narrowed** to invocation and construction.

Representative applications:

- explicit call inputs run in source order;
- omitted defaults run afterward in selected-prototype order;
- construction input evaluation remains distinct from member construction order;
  and
- result expressions establish slots left to right.

#### 4. Preference must be defensible, not guessed

> Zax selects one interpretation only when it is demonstrably better under the
> applicable rules. Incomparable or equal interpretations require explicit
> disambiguation rather than scoring tricks or source-order choice.

Recovered from raw statements `4` and `5`. It is broader than one overload table
because future generic, conversion, operator, and structural matching may need
the same discipline. The current function owner preserves its first complete
application.

#### 5. Context may clarify intent, but it must not invent it

> Explicit surrounding information may select among meanings when it states what
> the programmer wants. Zax does not recursively search unrelated conversions or
> later uses to manufacture intent.

Recovered from raw statements `16` and `17` and the result-selection discussion.
This is a reusable matching/API principle currently **captured only as an
invocation application**.

Representative applications:

- a complete `Integer` declaration can select an `Integer` result family;
- `:=` does not inspect later uses;
- unresolved operators do not speculate about an inner call's result; and
- destination constructor graphs do not choose a result overload.

#### 6. Costs follow chosen behavior and remain discoverable

> Programs should pay for the behavior they use, and consequential work should
> remain discoverable where it is introduced.

Recovered from raw statements `20`, `21`, and `23`. The foundational cost
principle is currently **fully owned by the vision**. If the catalog is adopted,
move the complete reusable principle into the catalog and retain only an
orienting summary and link in the vision. Do not create an inward catalog link
that leaves the vision as the exceptional full principle owner.

Representative applications:

- result slots do not default-construct unless requested;
- relabeling a compatible static function need not add runtime dispatch;
- captured defaults may require stored state; and
- async, synchronization, copy, move, and temporary costs remain visible.

#### 7. Optimization preserves the programmer-visible operation

> Optimization may remove storage or work only when the programmer-visible
> operation and its observable consequences remain unchanged.

Recovered from raw statement `22`. This is a language-wide semantic principle
currently **narrowed** to invocation and construction.

Representative applications:

- liveness may optimize a selected move but may not choose move semantics;
- temporary storage may be elided without changing lifetime effects; and
- result construction may be fused without changing ordering or destruction.

#### 8. Contracts and implementations are separable only without hidden adaptation

> A visible contract may improve names, defaults, and caller obligations while
> reusing a compatible implementation. If executable transformation is needed,
> the adaptation must be explicit.

Recovered from raw statements `18` and `19`. This is a reusable API-design
principle currently **fully expressed in the function owner** but worth
collecting for human discovery.

Representative applications:

- compatible function prototypes may relabel ordered slots;
- call-boundary defaults belong to the visible contract; and
- reordering or converting values requires a wrapper or lambda.

#### 9. Values do not silently become a different shape

> Several values remain several values until source explicitly combines or
> transforms them into one value.

Recovered from raw statements `11` and `12`. This is a reusable data-flow
principle currently **fully expressed for function results** and preserved as
future structural pressure.

Representative applications:

- multiple results are not an implicit tuple;
- forwarding results to constructor parameters does not populate stored members;
  and
- future structural promotion must be explicit.

#### 10. Feasibility informs design without defining source semantics

> Parser, tooling, analysis, and implementation feasibility are legitimate
> language-design pressures. Lasting language documentation still states the
> programmer-visible behavior rather than a compiler mapping.

Recovered from raw statements `24` and `25`. This is a language-design principle
currently **working-only in its positive form**. It belongs in the catalog's
design-facing section and should also be reflected in project documentation
boundaries.

This entry primarily serves programmers evaluating why Zax has a particular
shape and contributors designing future concepts. It should be separated from
the catalog's everyday source-use principles rather than presented as an
ordinary coding rule.

### Important local principles that remain with concept owners

These insights are important, but their useful meaning is inseparable from one
domain. The principles catalog may link to them as applications; it should not
promote each rule into a foundational slogan.

| Raw # | Local principle | Current owner |
| --- | --- | --- |
| 6 | Parsing a call does not depend on the current overload set | [Function invocation](../../../language/function-invocation.md#whitespace-and-intent) |
| 9 | Omission and explicit type-default initialization are distinct | [Function invocation](../../../language/function-invocation.md#omitted-inputs-and-defaults) |
| 10 | Inputs may be selected/defaulted flexibly, but every normal result is complete | Function mental model needs one plain statement; detailed behavior is current |
| 13 | Parentheses choose one expression at a multiple-result boundary | Function invocation and source structure |
| 14 | Comma and `\` express different continuation intent | [Source structure](../../../language/source-structure.md#comma-list-continuation) |
| 15 | Redundant continuation punctuation is an intent error | Source structure |

Raw statement `21` also has a local result application—no automatic
default-construction penalty—but its broader cost meaning belongs under catalog
principle 6.

### Language-design process guidance

These statements are principles for conducting design work, not principles a
programmer needs in order to understand Zax source. They remain in
[documentation architecture](../../documentation.md) or need a targeted addition
there:

| Raw # | Guidance | Current disposition |
| --- | --- | --- |
| 26 | Follow material consequences wherever they lead | Current |
| 27 | Do not lose findings whose owner lies outside the active work item | Current |
| 28 | A deferral records why it matters and what reopens it | Current |
| 29 | A contradiction cannot be hidden through deferral | Current |
| 43 | A disposition ledger supports understandable findings; it does not replace them | Needs an explicit documentation-architecture addition |

The design-pressure half of raw `24` belongs in the human-facing catalog because
it explains how Zax is evaluated. The cooperation and repository-placement
details remain project guidance.

### Documentation principles

These statements govern how Zax is taught and documented. They belong in
[documentation architecture](../../documentation.md), not the language principles
catalog:

| Raw # | Documentation principle | Current disposition |
| --- | --- | --- |
| 30 | Promotion teaches the selected model rather than replaying the design meeting | Current |
| 31 | Examples are essential teaching tools | Needs a direct statement |
| 32 | Concepts are introduced in prerequisite order | Needs a direct statement |
| 33 | Assume a competent programmer while explaining Zax-specific behavior clearly | Needs a stronger positive statement |
| 34 | Teach Zax directly | Current |
| 35 | Compare another language only to resolve a concrete misunderstanding | Current |
| 36 | Language-specific primers are separate future reader routes | Current |
| 37 | Do not teach rejected bad ideas merely to reject them | Current |
| 38 | Teach accepted surprising boundaries with counterexamples | Current |
| 39 | Simple teaching does not mean omitting important detail | Current |
| 40 | Layer detail instead of making the whole document dense | Current |
| 41 | Future specification work should formalize rather than rediscover | Current |
| 42 | Working records and owner documents serve different purposes | Current |

### Complete raw-statement disposition

Every statement from the original list remains accounted for:

| Raw statements | Revised disposition |
| --- | --- |
| `1`-`5`, `7`, `8`, `11`, `12`, `16`-`25` | Contribute to the ten candidate human-facing catalog principles above |
| `6`, `9`, `10`, `13`-`15` | Important local principles retained in function/source owners |
| `26`-`29`, `43` | Language-design process guidance in documentation architecture |
| `30`-`42` | Documentation and teaching principles in documentation architecture |

No raw statement is discarded. A statement may contribute both to a reusable
catalog principle and to a precise local application.

### Proposed documentation fit

This audit recommends a supplemental pre-promotion documentation fit dry run for
principle ownership before `006` closes. The likely change set is:

1. create `language/principles.md`;
2. update [language vision](../../../language/vision.md) to summarize and route to
   the catalog while narrowing its `Owns` field from the complete principles
   catalog to foundational purpose/direction and a principles summary;
3. disposition every current `Design principles` subsection in the vision as
   stay, migrate, or summarize-and-link, including explicit treatment of
   `Visible and selectable cost` and
   `Familiarity, readability, and elegance`;
4. update the public [website entry point](../../../index.md);
5. add the missing process and teaching guidance to
   [documentation architecture](../../documentation.md);
6. add the plain flexible-inputs/complete-results statement to
   [function invocation](../../../language/function-invocation.md); and
7. link local concept owners to catalog principles only where a concrete
   dependency helps a reader.

The fit dry run must verify that:

- the vision and principles catalog do not become competing principle owners;
- every existing vision design-principle subsection has a declared stay,
  migrate, or summarize-and-link disposition;
- the catalog owns every complete reusable principle entry, including visible
  and selectable cost;
- the catalog remains human-facing rather than a project-process document;
- exact behavior remains with concept owners;
- the catalog can accept future aligned principles without becoming a slogan
  dump; and
- principles discovered by future numbered work receive explicit disposition.

### Revised audit verdict

The technical `006` promotion remains coherent, but the broader insight-capture
goal is not complete.

Work item `006` should remain open until:

1. the proposed human-facing principles catalog and owner boundary are discussed;
2. a supplemental documentation fit dry run confirms the structure and exact
   change set;
3. the selected principles and documentation additions are aligned; and
4. any resulting wider-document edit is separately authorized and validated.

No principles-catalog or wider-document edit was authorized or applied by this
revised audit alone. The later authorized promotion is recorded below.

## Supplemental principles documentation fit and promotion

| Field | Value |
| --- | --- |
| Date | 2026-08-24 |
| Dry-run result | Successful; a dedicated catalog can own reusable principles without competing with vision or concept owners |
| Promotion status | Aligned, authorized, and applied |
| New current owner | `language/principles.md` |

### Vision principle disposition

The supplemental fit check dispositioned every prior
`language/vision.md` design-principle subsection:

| Existing vision subsection | Disposition |
| --- | --- |
| High-level expression without hidden fundamentals | Full reusable principle moved to the catalog; vision summarizes and links |
| Visible and selectable cost | Full reusable principle integrated into the catalog's cost principle; vision summarizes and links |
| Explicit policy rather than one compulsory policy | Full reusable principle moved to the catalog; vision summarizes and links |
| Data-oriented organization and composition | Stays in vision as foundational language direction |
| Language-integrated build-time execution | Stays in vision as foundational feature direction |
| A bounded safe subset with explicit responsibility | Stays in vision as foundational direction and tradeoff |
| Multiple lifetime strategies | Stays in vision as foundational feature direction |
| Familiarity, readability, and elegance | Full reusable principle moved to the catalog; vision summarizes and links |
| First-class asynchronous programming | Stays in vision as foundational feature direction |
| Direct assembly and selected foreign interoperability | Stays in vision as foundational feature direction |
| Self-contained and durable builds | Stays in vision as foundational direction and tradeoff |

The catalog owns complete reusable principle entries. The vision owns purpose,
audience, goals, foundational feature direction, non-goals, and high-level
tradeoffs, and provides a concise route to the catalog. Exact behavior remains
with concept owners.

### Promoted principles catalog

`language/principles.md` now collects thirteen human-facing principles:

1. Expressiveness serves clear intent.
2. Similar meaning should look similar.
3. Visible order should be trustworthy.
4. Preference must be defensible, not guessed.
5. Context may clarify intent, but it must not invent it.
6. Costs follow chosen behavior and remain discoverable.
7. Optimization preserves the programmer-visible operation.
8. Contracts and implementations separate only without hidden adaptation.
9. Values do not silently become a different shape.
10. High-level expression does not hide fundamentals.
11. Policies are selectable rather than compulsory.
12. Familiarity serves readability.
13. Feasibility informs design without defining source semantics.

Each entry states the principle, why it matters, representative applications,
and an important boundary or tension. The catalog links exact owners rather than
duplicating their rules.

### Documentation and local-owner corrections

Promotion also:

- updated the public index to route readers to the principles catalog;
- updated documentation architecture's repository-surface table so the
  principles catalog, rather than the vision, owns complete reusable principle
  definitions;
- strengthened documentation architecture with the competent-programmer
  baseline, implementation-feasibility pressure, essential examples,
  prerequisite teaching order, and the rule that ledgers support rather than
  replace understandable findings; and
- added the plain flexible-inputs/complete-results mental model and concrete
  principle links to the function-invocation owner.

### Exact supplemental promotion set

The authorized principles promotion changed exactly:

1. `language/principles.md` — created;
2. [language vision](../../../language/vision.md) — ownership realigned and
   reusable principles summarized/linked;
3. [website entry point](../../../index.md) — current principles route added;
4. [documentation architecture](../../documentation.md) — missing process and
   teaching guidance added;
5. [function invocation](../../../language/function-invocation.md) — local
   application and complete-result mental model added; and
6. this active work record — supplemental fit, disposition, and outcome recorded.

### Supplemental success assessment

The principles promotion is successful:

- the central expressiveness-and-intent insight is preserved at foundational
  human-facing weight;
- the catalog has a distinct reader task and lifecycle;
- vision and catalog ownership no longer overlap fully;
- all prior vision principle sections have an explicit disposition;
- exact semantics remain in concept owners;
- process and documentation guidance remain outside the language catalog;
- all 43 raw statements retain a disposition; and
- future numbered work has a known place to propose newly discovered reusable
  principles.

The language maintainer subsequently authorized closure, archival, and
preparation of work item `007`. Staging, committing, and pushing remained
separate actions.

## Closure record

Work item `006` closed after:

- promoting ordinary function invocation, argument/default binding, results,
  routing, fixed-arity selection, compatible visible prototypes, synchronous
  completion, diagnostics, costs, and formatting into current owners;
- creating the human-facing reusable language-principles catalog and realigning
  vision ownership;
- updating affected declaration, qualifier, construction, source-structure,
  terminology, documentation, router, legacy, and raw surfaces;
- retiring consumed raw function-invocation input after preserving every useful
  finding and provenance path;
- validating the authorized promotion sets; and
- preparing work item `007` for core flow-control review without beginning its
  analysis.

Current documents own all accepted behavior. Explicit deferrals remain in their
indexed raw inputs or current owner boundaries. This archived file exists only
for targeted discovery and audits.
