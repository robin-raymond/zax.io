# Zax function invocation

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax calls |
| Applies To | Programmer-facing synchronous function invocation, argument and default binding, results, and callable selection; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | Ordinary call syntax; visible callable contracts; bound/unbound callable invocation and installed receiver use; instance and narrow type-callable `once` invocation; the parameter/argument distinction; type parameter slots and type arguments at the shared callable depth; concrete result specialization before literal invocation; positional, named, omitted, type-default, and contextual construction-packet inputs; transfer-aware value/reference binding, including composition preferred projection after an expected type exists; compatibility-posture and source-anchor call/result mapping; evaluation and binding order; ordinary and exceptional completion shapes; result slots, stance, completion, destination ordering, conditional publication, and elision; the callable-facing `self` result contract; multiple-result expression and mapping modes; operator result integration; result routing, including structural decomposition, recomposition, transforming groups, catch destinations, and exceptional forwarding; fixed-arity overload viability and preference; receiver-slot comparison; minted concrete implementations and compatible visible-prototype adaptation; preservation of declaration-side replacement permission through mapping, results, and captures; synchronous call completion; `operator call` input/result mapping; `inline` hints; caller-location defaults; call/index mixfix parameter segmentation at the shared callable depth; invocation diagnostics, costs, and formatting |
| Does Not Own | The cohesive exceptional-flow model ([exceptional result flow](except.md)); complete transfer meaning ([transfer stances](transfer-stances.md)); uncommitted integer evaluation and realization ([integer literals and realization](integer-literals.md)); literal source, declaration, payload, merge, and join behavior ([literal source and operators](literal-source-and-operators.md)); complete [optional behavior](optional-values.md); lambda/capture representation and callable composition ([lambdas and callable composition](lambdas-and-callable-composition.md)); composition publication, exposure, and route eligibility ([Zax composition](composition.md)); `using` resource enrollment and disposal ([Zax `using`](using.md)); operator forms and selection ([operators](operators.md), [operator catalog](operator-catalog.md)); or complete [reference origin and lifetime](lifetimes-and-references.md) |
| Source / Provenance | Legacy function material together with current declaration, qualifier, construction, and source-structure constraints |

## Mental model

An ordinary call names or produces a callable and supplies its inputs:

```zax
print(message)
```

The selected callable's prototype declares its result slots first and its input
parameters second:

```zax
double final : (
  result : Integer
)(
  input : Integer
) = {
  return input * 2
}

doubled := double(21)
```

A synchronous call proceeds in understandable phases:

1. Select one available callable without executing runtime argument expressions.
2. Evaluate the callee and receiver operand once.
3. Evaluate explicit inputs in caller source order and immediately bind each one.
4. Evaluate defaults for still-unbound parameters in the selected callee
   prototype's parameter order.
5. Initialize any result slots that opt into pre-body construction.
6. Execute the body.
7. Select and complete either the ordinary success shape or one declared
   exceptional outcome.
8. Map or transfer only that selected outcome into the surrounding context.

The body cannot observe a partially bound parameter list. Every parameter is
complete before body entry.

Zax permits flexible call forms when programmer intent remains clear. An
ambiguous call is an error until the programmer makes the intended operation
explicit.

This applies the broader
[Expressiveness serves clear intent](principles.md#expressiveness-serves-clear-intent)
and [Preference must be defensible, not guessed](principles.md#preference-must-be-defensible-not-guessed)
principles.

Inputs may be selected, labeled, omitted, or defaulted flexibly. Every
invocation completes one full declared outcome: all ordinary success results or
one named exceptional result. The programmer-facing model is taught by
[Zax exceptional result flow](except.md).

## Callable prototypes and visible contracts

Parameter and result declarations form a callable's visible source contract.
Their:

- ordered slots;
- names and labels;
- types and qualifications;
- transfer stances;
- input defaults;
- result-acknowledgement policy; and
- [execution policy](compile-time-code.md#letting-a-function-decide-execution-policies),
  which says whether calls may or must run while compiling

affect how callers can use the callable.

Function values use the same call surface as directly selected declarations:

```zax
declaredFunction(input)
functionValue(input)
instance.memberFunction(input)
```

A resolved function value supplies one prototype. It does not dynamically search
declarations that happen to share its source name.

### Bound and unbound callables

A bound implementation executes with an installed receiver available through
`_`. An unbound implementation has no receiver:

```zax
Utilities :: type {
  compare final : (result : Boolean)(
    lhs : String readonly &,
    rhs : String readonly &
  ) unbound = {
  }
}
```

`Utilities` owns the declaration, but invoking `Utilities.compare` evaluates no
instance receiver and `_` is unavailable inside the body.

`type of` inspects the callable type without evaluating the instance expression:

```zax
myValue : MyType
BoundPrototype :: alias type type of myValue.process
```

The type query does not create a runtime value with an unfilled receiver. A
member expression such as `myValue.process` forms the bound callable value.
Receiver-capable varying storage may later replace it with another compatible
unbound or bound implementation.

Declaration defaults, `final` storage, and the `once final unbound` intent error
are defined by
[declarations and bindings](declarations-and-bindings.md#bound-and-unbound-function-storage).
Lambda-generated receivers, installed binding kinds, and receiver lifetime are
defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#callable-prototypes-and-storage).

### Default unavailable function values

Default initialization establishes a live but unavailable function value as
defined by
[Zax declarations and bindings](declarations-and-bindings.md#default-function-values).

The compiler diagnoses an invocation it can prove still targets that default
state. An otherwise unhandled invocation panics. It does not execute a no-op,
manufacture arbitrary results, or invoke an unrelated overload.

One deliberate exception is a `bound weak` callable whose complete result
contract is empty: it has neither ordinary nor exceptional results. An
unavailable slot and a failed temporary strong promotion both complete as no-ops
rather than panicking. Any `except` result makes the callable resultful and
therefore ineligible for weak storage. Explicit arguments, defaults, parameter
construction, and other caller-side setup still occur under the visible
prototype before no body runs. This is the declared weak callable contract, not
ordinary unavailable-function behavior. Complete behavior belongs to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#weak-invocation-is-conditionally-empty).

Recognized function presence returns exactly `Boolean`. A known fixed `final`
function is statically present; a varying function value may require a runtime
presence observation:

```zax
if ?callback
  callback()
```

`reset callback` releases the callable representation and any owned captures,
then restores the unavailable state. Complete presence, reset, and relationship
to type Nothing behavior is defined by
[Zax Nothing instances](nothing-instances.md#function-values-have-presence).

### Receiver operands

A type-defined function has a receiver operand representing the instance on
which it acts:

```zax
document.save(path)
```

The receiver expression evaluates once before explicit arguments:

```zax
makeDocument().save(makePath())
```

`makeDocument()` completes before `makePath()`.

Receiver type and qualification participate in candidate selection. Inside the
body, current-instance member access remains explicit through `_`:

```zax
Document :: type {
  save final : ()(
    path : Path
  ) readonly = {
    writeTo(path, _.contents)
  }
}
```

Complete receiver qualification is defined by
[Zax qualifiers](qualifiers.md#receiver-operands).

### Type and instance calls to `once` functions

A type-owned `once bound` function may be selected through either the type or an
instance:

```zax
MyType.inspect()

value : MyType
value.inspect()
```

For `MyType.inspect()`, the type identity supplies member lookup without a
receiver source expression. This is a **receiverless type call**. `_` identifies
`MyType`'s Nothing instance and `?_` is false. The implementation may perform
type-level work, read compiler-prepared members, use custom Nothing behavior, or
branch on receiver presence. Trapping or unprepared member access uses the
available generated panic or hardware trap; missing target trap capability has
the undefined boundary defined by the Nothing owner.

For `value.inspect()`, `value` evaluates once before the explicit arguments and
supplies the ordinary receiver. `_` identifies that instance and `?_` is true.

Both forms select the same declared callable and explicit parameter/result
contract. For a `final` function, `once` adds the type-qualified
receiverless call rather than another implementation slot. `once` does not
create an overload tie between a hidden type-callable implementation and an
instance implementation.

The receiverless route synthesizes a `copy`-stanced Nothing receiver before
selection. An exact `last` receiver declaration therefore remains available to
an instance source but not to the type call:

```zax
(value as last).consume()
MyType.consume() // error when consume requires exact last receiver
```

There is no runtime change from `last` to `copy`. Aliases, forwarding, and
bound callable values preserve whether they represent receiverless
lookup or an instance receiver.

`_` is pointer-shaped, while `_.` supplies the dereferenced form. Mapping to an
expected pointer prefers `_`; mapping to an expected `MyType` may use `_.`.
Either form may be passed or returned, including through a verified `self`
result. Pointer dereference remains unchecked unless the type selected trapping
Nothing behavior.

Declaration, `final`, generated default, replacement, and prohibition behavior
is defined by
[declarations and bindings](declarations-and-bindings.md#type-callable-once-functions).
Complete Nothing policies and receiver access behavior are defined by
[Zax Nothing instances](nothing-instances.md#receiverless-and-instance-calls).

This call rule does not define global or `once` value initialization, capture,
concurrency, teardown, or generic-specialization behavior.

### `operator call`

Call syntax is a postfix delimited form rather than an ordinary post-unary
operator:

```zax
callable(arguments)
```

A declared function is implicitly callable. A custom callable type may define a
complete call operation:

```zax
// Illustrative declaration syntax.
operator call final :
  (result : Result)(
    number : Integer,
    text : String = "default"
  ) = {
}
```

The input prototype supplies argument count, labels, defaults, qualifications,
and transfers. Its result prototype uses ordinary result mapping.

Inside a flattened mixfix declaration, `call N` states how many prototype
inputs belong to one call component. Each index bracket instead contributes one
`index 1` component:

```zax
// Illustrative mixfix declaration fragment.
operator mixfix
  call 2
  binary '+'
```

Omitted `:` still occupies one call slot and requires the mixfix parameter's
declared default. A consumed call/index component produces no intermediate
call result or proxy; the complete mixfix owns the final result shape.

Exact default timing across a flattened mixfix, variadics, result forwarding,
lambda/generated callable types, construction-like call syntax, and indexing
details remain future integration. Complete tree behavior is defined by
[mixfix operators](mixfix-operators.md#call-index-and-splice-components).

### Inlining hints

A call normally jumps to the function's code and returns. Inlining instead
copies a function's body into each caller, which avoids the call overhead and
can enable further optimization, at the price of larger code. A compiler
decides this by itself, and a `final` function may express a preference with
`[<inline>]` in the slot after `final`:

```zax
smallHelper final [<inline>] : (result : Integer)(value : Integer) = {
  return value + 1
}

hotPath final [<inline=always>] : ()() = {
}

coldPath final [<inline=never>] : ()() = {
}
```

| Form | Meaning |
| --- | --- |
| no directive | `maybe`: the compiler decides |
| `[<inline>]` | `likely`: prefer inlining |
| `[<inline=unlikely>]` | prefer an ordinary call |
| `[<inline=always>]` | inline wherever possible |
| `[<inline=never>]` | keep every call an ordinary call |

Every level is a [hint](compiler-directives.md#what-a-directive-may-promise):
inlining never changes what a call does, and it is never an error when inlining
is impossible. `never` can always be honored, because a call can always remain
a call, so compilers honor it. `always` is the strongest request: a recursive function or a call
through a function value cannot always be inlined, and the compiler does what
it can, for example by inlining a local recursive helper per use. Variables
declared in an inlined body stay private to it, exactly as in an ordinary call.

## Parameters, arguments, and type arguments

Parameter and argument are distinct:

- a **parameter** is a slot declared by a prototype;
- an **argument** is caller source supplied to complete a parameter; and
- an operator operand supplies an argument to one selected parameter, exactly as
  a call input does.

### Type parameter slots

A prototype may declare a **type parameter slot**, and a caller completes it with
a **type argument**: one concrete type identity.

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

A type argument:

- has no runtime storage or lifetime;
- is not evaluated at runtime;
- occupies its parameter slot for viability, preference, and diagnostics like any
  other input; and
- may determine a value result type, as it does for `as`.

Type parameter slots therefore participate at the shared callable depth rather
than being an operator-only concept. A concrete type argument is not a runtime
value and does not become one merely because a value receiver supplied
discovery.

Declaration ownership, type-qualified `unbound`/`once` operators, and complete
generic behavior are defined by
[declarations and bindings](declarations-and-bindings.md#operator-phrase-declarations-and-type-parameters).

### Literal result specialization

A generic literal result is specialized before its body is invoked:

```zax
myDefault := h'FF'  // selected result UInteger
myByte : U8 = h'FF' // selected result U8
```

The literal declaration supplies one generic result slot, not several
result-only overloads. Compile-time/generic processing uses its payload,
candidate hole, sign intent, and suggestion to select one concrete result
prototype. The body is processed and invoked under that prototype.

Ordinary invocation then sees exactly the same contract as a declaration that
had stated the concrete type directly. An `uncommitted` generic placeholder:

- is not a runtime or anonymous result;
- cannot enter ordinary result routing or an enclosing operator;
- does not let value magnitude rank overloads;
- does not reopen after invocation; and
- does not retry another result type after selected range failure.

Exact generic specialization syntax and compile-time type-selection mechanics
remain future work. Complete programmer-facing literal behavior is defined by
[Zax literal source and literal operators](literal-source-and-operators.md#every-invocation-has-one-concrete-result).

### Declaration-side replacement permission

Qualification travels with an argument, and declaration-side replacement
permission is part of that truth. A path that may not replace its own value
lifetime cannot regain that authority by being mapped into a parameter,
forwarded through a result, or captured:

```zax
replaceThrough final : ()(target varying : MyType immutable writable varying &)

restricted final : MyType immutable writable varying & = value
replaceThrough(restricted) // error: restricted may not delegate replacement authority
```

The referent type use still records that another path may replace the referent;
only this declaration's permission is narrower. Complete capability-versus-permission
behavior is owned by
[qualifiers](qualifiers.md#type-side-truth-versus-declaration-side-permission).

### Transfer-aware value and reference parameters

A transfer stance first changes which parameter contract the argument may
satisfy. The stance expression performs no transfer because no consumer has been
selected or run yet. After selection, the parameter's binding form determines
which object the function may change.

The ordinary contracts are:

```zax
read final : ()(
  input : Message readonly & copy
) = {
}

consume final : ()(
  input : Message mutable writable & move
) = {
}
```

A reference-shaped `move` or `last` parameter refers directly to caller-owned
storage. The selected function may take ownership of resources from that value
during the call:

```zax
consume final : ()(
  input : Message mutable writable & move
) = {
  send(input.payload as move)
}
```

A by-value `move` or `last` parameter instead constructs an independent
parameter first. The caller's source is preserved by that copy-in barrier;
resource transfer affects the local parameter:

```zax
consume final : ()(
  input : Message move
) = {
  send(input.payload)
}
```

By-value `deep` performs `deep` construction at the binding boundary and retains
`deep` stance in the body. By-value `copy` performs ordinary `copy`
construction.

For an ordinary writable source and otherwise equal `move`/`last` candidates, the
normal mutable/writable reference form is better than by value. A readonly
restatement can make that reference nonviable:

```zax
consume((message as readonly) as move)
```

If a readonly-reference variant also remains viable, neither candidate may be
better overall: the reference may avoid copying, while by value preserves
caller-owned storage. The later selection model calls this **incomparable**. The
call is ambiguous rather than silently choosing one ownership effect.

Complete stance meaning, fallback, projection, and source post-state are defined
by [Zax transfer stances](transfer-stances.md).

### Array values, bounded references, and slices

An owning array by-value parameter constructs an independent local array:

```zax
consume final : ()(
  values : Integer[2..10]
) = {
}
```

The caller's array must currently contain between two and ten elements. If the
compiler already knows that it does not, the call is an error. Otherwise the
call checks the current length and panics before entering the body when it falls
outside that range.

The local parameter is a newly constructed owning array. The source's
`copy`, `deep`, `move`, or `last` stance determines how that construction uses
the caller's elements and storage.

When the parameter names a provider instance, the local array uses that
provider. When it names only `storage type MyStorage`, construction obtains a
provider of that type. With no storage requirement, construction may reuse a
compatible source provider or choose the destination context's default.

A source array using another provider remains acceptable because this by-value
boundary constructs new destination storage.

A bounded array reference may adapt another intrinsic array while retaining its
underlying bounds and storage capabilities:

```zax
change final : ()(
  values : Integer[0..10] &
) = {
  values.resize(10)
}
```

The call first checks that the array's current length fits `0..10`. The
reference still remembers what the original array can actually do. A later
resize must satisfy both the parameter's written limits and the original
array's limits.

Remembering those two sets of limits may require extra metadata, so this
adapting reference can cost more than a reference to one exact array type.

An array reference without a storage requirement is also storage-erased. Inline
arrays and arrays using different provider instances/types can bind to the same
concrete function implementation. The reference carries the common operations
needed to locate elements through the source storage and may therefore be wider
or more indirect than an exact storage-qualified reference.

Binding such a reference creates no second array and copies no elements. A
reference naming an exact provider type or instance accepts only a source using
that storage because no destination storage is constructed.

The source's complete declared length range need not fit inside the parameter
range. Binding checks the current length. Later writable size changes check both
the parameter's visible range and the source's actual range and may panic when
only one accepts the request.

A readonly array reference cannot change length, so after its entry check it
does not need the source to support every size named by the parameter. A future
no-panic callable capability may request stronger static range containment;
ordinary writable references remain dynamically adapting.

A by-value `T[]` parameter copies only the slice's window description; it does
not copy the viewed elements. `T[] &` refers to that description and may bind
to a temporary slice created from an array for the duration of the call.
Complete identity, construction, storage selection, qualification, and failure
behavior belongs to
[Zax arrays and slices](arrays-and-slices.md#array-and-slice-parameters).

### Preferred composition projection

Preferred projection begins only after normal callable discovery has found a
candidate and that candidate supplies an expected input type:

```zax
tune final : ()(target : Engine readonly &) = {
}

Car :: type {
  engine preferred : Engine
}

car : Car
tune(car) // projects the preferred Engine member
```

The call first finds `tune`; it does not search through `car`. The selected
prototype then says that this input needs an `Engine readonly &`. Because `car`
does not match directly, its `preferred` member may supply `car.engine`.

An exact input match always beats preferred projection. If several projected
routes remain equally viable, the call is ambiguous rather than choosing by
declaration order. Projection also does not invent a target for generic
deduction or structural matching.

This is an input-mapping step, not callable or operator discovery. Complete
route declaration, qualification, nesting, and ambiguity behavior belongs to
[Zax composition](composition.md#expected-type-projection-with-preferred).

### Structural compatibility posture

After discovery supplies one concrete parameter type, an argument's
compatibility posture may make a safe binary-compatible recast viable. Exact
identity remains better than posture-based adaptation. Posture never widens
callable or operator discovery, invents an expected type, or ranks several
non-identity conversions by apparent closeness.

A local parameter receives the posture written on its own declaration after
binding; it does not inherit the caller's posture automatically. Complete
posture and anchor behavior belongs to
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#compatibility-posture).

An explicit composition adapter uses the same projection rule when its visible
outer parameter must map to a contained input:

```zax
Tank :: type {
  fuel preferred : Fuel

  refill final : ()(source : Tank) = via fuel.refill
}
```

The adapter may map `source` to `source.fuel` automatically because `fuel` is
`preferred`; `own` alone would not authorize that mapping. A written body may
always pass `source.fuel` explicitly without declaring a preferred route.

## Positional and named inputs

An ordinary positional input is an expression:

```zax
send(stream, bytes)
```

A named input uses the parameter label:

```zax
send(stream, encoding: utf8, bytes)
```

Parameter labels are source-facing contract elements. Renaming a public label can
break labeled callers even when the callable's executable types remain
compatible.

### Positional cursor

Input mapping is tested separately for each callable candidate:

1. The positional cursor starts at the first parameter.
2. A positional input maps the current parameter and advances the cursor.
3. A named input maps that label and resets the cursor to the parameter after it.
4. A parameter may be mapped at most once.
5. A positional input never skips the current parameter merely because that
   parameter has a default.

Given:

```zax
configure final : ()(
  host : Host,
  retries : Integer = 3,
  verbose : Boolean
) = {
}
```

this does not skip `retries`:

```zax
configure(host, true) // true maps to retries, then fails by type
```

The caller can acknowledge the omitted slot or use a later label:

```zax
configure(host, :, true)
configure(host, verbose: true)
```

A later positional input follows the most recent named input:

```zax
configure(host: host, 3, true)
```

Here `3` maps to `retries` and `true` maps to `verbose`.

### Explicit positional intent

Leading `:` marks an input as explicitly positional:

```zax
send(: stream, bytes)
```

It is normally unnecessary for an ordinary expression. It becomes useful when a
declaration expression or another form could otherwise resemble a label.

## Whitespace and intent

Whitespace does not create Zax grammar, but it must honestly present programmer
intent.

In a label-capable position:

```zax
encoding: value   // label
encoding : Type   // declaration
```

A bare declaration expression at the top level of that position is an intent
error because it can easily be a mistyped label:

```zax
send(stream, encoding : Encoding, bytes) // error
```

Group the declaration expression or make its positional intent explicit:

```zax
send(stream, (encoding : Encoding), bytes)
send(stream, : encoding : Encoding, bytes)
```

A named input already establishes its outer label:

```zax
send(stream, encoding: local : Encoding, bytes)
```

This rule depends on source form, not the current overload set. Adding or
removing an overload cannot reinterpret the parse.

## Argument declaration expressions

An input expression may introduce a binding:

```zax
send(
  : temporary : Buffer,
  inspect(temporary)
)
```

The binding:

- becomes visible after its initialization completes;
- is available to later explicit inputs in the same call;
- survives through call completion; and
- does not escape into the following statement merely because it has a name.

Grouping supplies the same declaration-expression boundary:

```zax
send((temporary : Buffer), inspect(temporary))
```

Ordinary declaration and visibility behavior is defined by
[Zax declarations and bindings](declarations-and-bindings.md).

### Construction packets as arguments

A bare construction packet has no independent expression type. Once callable
mapping identifies one concrete expected parameter type, however, that parameter
supplies the packet's destination:

```zax
draw final : ()(point : Point &) = {
}

draw(point: [{ .x = 4, .y = 5 }])
draw([{ .x = 4, .y = 5 }]) // positional form
```

This constructs an actual `Point` temporary and binds it through the selected
parameter. It is not an anonymous structural value or a posture-based
conversion.

Candidate selection tests packet viability independently for each already
discovered parameter type. If several callable candidates remain equally viable,
the call is ambiguous rather than letting the packet invent a destination.
Nested packets receive their destinations recursively from the selected
construction plan.

The explicit typed forms remain available:

```zax
draw((: Point = [{ .x = 4, .y = 5 }]))
draw(point: (: Point = [{ .x = 4, .y = 5 }]))
```

`(: Point = ...)` is an anonymous typed declaration expression. The
`point:` in the final call is the named parameter mapping; the two roles are
independent.

Complete packet behavior is defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md#construction-packets).

## Evaluation and immediate binding

Explicit value-producing inputs evaluate strictly in caller source order. Each
one immediately initializes or binds its already mapped parameter before the
next input begins:

```zax
source : Integer = 1

observe(
  source,
  action: changeSourceToTwo(source)
)
```

If the first parameter copies, it captures `1`. If it references `source`, it
binds before the mutation but may observe `2` when the body later reads the
referent.

A by-value `move`, `last`, or `deep` parameter performs its required construction
during this immediate binding step. A reference-shaped `move` or `last`
parameter immediately refers to existing storage, but its selected body may take
resources later during the call.

Producing `source as move`, `source as last`, or an optional phrase form has no
effect by itself because it only changes the stance offered to selection. The
selected consumer decides what transfer, if any, occurs.

When a consumer accepts `last optionalValue`, the optional immediately enters
terminal source state for analysis but keeps required boxed state live until the
complete consumer finishes. Complete optional state and cleanup behavior is
defined by
[Zax optional values](optional-values.md#move-and-last-source-adapters).

This order is programmer-visible. An optimization may elide storage or operations
only when it preserves:

- callee, receiver, and argument side effects;
- selected candidates;
- copied values and bound referents;
- source availability after transfer;
- construction and destruction effects; and
- result production and mapping.

### Callbacks and reentrancy

A callback invoked while evaluating an input completes before the next input
starts. A callback invoked by immediate parameter construction is part of that
binding step.

Reentrant code may observe completed earlier effects. It must not observe a
fiction that later parameters are already bound.

## Omitted inputs and defaults

A parameter may declare a default expression:

```zax
connect final : ()(
  host : Host,
  attempts : Integer = 3,
  trace : Boolean = false
) = {
}
```

Omission uses that declared default:

```zax
connect(host)
connect(host, trace: true)
```

An empty input explicitly acknowledges omission:

```zax
connect(host, :, true)
connect(host, attempts:, trace: true)
```

The candidate is unavailable when an omitted parameter has no declared default.

### Default evaluation order

Empty omission entries map slots but perform no runtime evaluation. After every
explicit value-producing input completes, defaults for still-unbound parameters
evaluate in input-parameter declaration order in the selected callee's visible
prototype.

```zax
configure(
  third:,
  first: makeFirst(),
  second: makeSecond()
)
```

`makeFirst()` runs before `makeSecond()`. Only afterward does any omitted default
run.

This order allows deterministic dependent defaults:

```zax
operation final : ()(
  first : A,
  second : B = makeB(),
  third : C = makeC(second)
) = {
}
```

When both are omitted, `second` completes before `third`. A default may refer to
the receiver operand, applicable surrounding callable state, and earlier
parameters in prototype order. It may not refer to itself or a later parameter.

### Explicit type-default expressions

Omission and type-default initialization are different:

```zax
connect(host, attempts:, trace: true)  // declared default, evaluated later
connect(host, attempts: :, trace: true) // type-default Integer, evaluated here
```

The inner `:` is an actual expression producing the mapped parameter type's
default value. It runs at that source position and overrides any declared
parameter default.

The positional form is:

```zax
connect(host, : :, true)
```

### The caller's source location as a default

Because a default is caller-side setup, a default argument can report where the
call was written. `Compilation.caller` is valid only as a default argument, and
gives the call site's location:

```zax
trace final : ()(where : SourceLocation = Compilation.caller) = {
  print(where.file.path, where.line)
}

trace()   // reports this line, not a line inside trace
```

The [`Compilation`](compile-time-code.md#compilation-information) namespace
describes `SourceLocation` and the other compile-time facts.

## Visible defaults and compatible implementations

Defaults belong to the visible callable contract used at the call site:

```zax
callback : ()(
  timeout : Duration = configuredTimeout()
) = implementation
```

Calls through `callback` use `callback`'s label, default, types, and
qualifications. Defaults declared on `implementation` remain part of
`implementation`'s direct call contract and are not layered under
`configuredTimeout()`.

The default expression evaluates once for each actual omission. A captured
environment or retained state needed by that delayed expression is a cost of the
visible contract.

A raw pointer parameter may allocate its omission default:

```zax
useValue final : ()(
  value : MyValue * = @
) = {
  // value is borrowed raw; the body owns no cleanup obligation.
}
```

`= @` is a default expression, not a cleanup contract on the parameter slot.
When the caller omits `value`, caller-side invocation machinery owns the
scheduled allocation temporary through call completion and binds its raw address
to the parameter. When the caller supplies a value, that source retains its
ordinary ownership or lifetime responsibility.

General default expressions preserve the same boundary:

```zax
useValue final : ()(
  value : MyValue * = makeScheduledValue()
) = {
}
```

If the producer returns a scheduled temporary, that temporary remains alive
through the call and is dispositioned afterward.

A terminally transferred open-ended default can also be adopted into an
invocation-owned cleanup obligation:

```zax
makeOpenValue final : (
  result : MyValue * last = @<
)() = {
}

useValue final : ()(
  value : MyValue * = makeOpenValue()
) = {
}
```

The caller-side boundary retains the allocation through call completion and
resets it afterward. This is valid only when the result carries allocation-root
and disposition authority. A global or ordinary borrowed result causes no
cleanup to be invented. The callee sees a borrowed raw parameter in every case.
Use a managed pointer parameter when ownership must transfer into the callee.

## Result slots

A selected prototype declares an ordered ordinary result shape:

- `()` has zero results;
- `(value : T)` has one result; and
- `(first : A, second : B)` has two results.

An `except` result instead declares one named exceptional completion shape:

```zax
read final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  // ...
}
```

One invocation publishes either ordinary `value` or exceptional `failure`,
never both. A callable with several `except` results has one separate completion
shape for each. Complete declaration, handling, forwarding, reshape, and
acknowledgement behavior is taught by
[Zax exceptional result flow](except.md).

At the current conceptual depth, exceptional outcomes belong to ordinary
function and call-syntax callable prototypes. Constructor, destructor,
replacement-constructor, and non-call operator result shapes cannot declare
`except`; their available results remain ordinary shapes.

Each ordinary result declaration establishes:

- an ordered result slot;
- a source-facing label, type, and qualifications; and
- a transfer stance that becomes active after construction; and
- an obligation to contain one complete value whenever the success outcome is
  selected.

The result place is available before body execution. Its resident instance may
be established before body entry when the prototype declares an initializer, or
the body may remain responsible for constructing it. An exceptional result
accepts no initializer and may not be manually constructed early; producer
`except` or explicit forwarding constructs it when selecting that outcome.

An ordinary result slot begins unconstructed:

```zax
make final : (
  result : Item
)() = {
  return source
}
```

`return source` directly constructs the slot. It does not default-construct an
`Item` and then assign over it.

An optional result therefore establishes absence or presence directly:

```zax
find final : (
  result : Item?
)(
  shouldReturn : Boolean,
  input : Item
) = {
  if shouldReturn
    return input

  return (: Item?)
}
```

Neither path first constructs an absent result and assigns over it. Complete
optional construction and transfer is defined by
[Zax optional values](optional-values.md#parameters-and-results).

A result's initializer or return expression decides how the result slot is
constructed. Once the slot exists, the stance written on its declaration decides
how later consumers see it:

```zax
make final : (
  result : Item move
)() = {
  return source as copy
}
```

`source as copy` constructs `result`. After construction, ordinary uses of
`result` in the body offer `move`, and the caller also receives a `move`-stanced
result.

A function always returns the types written in its selected prototype, even
when the compiler evaluates the call early. A number literal in `return` can
take that declared result type directly:

```zax
makeByte final : (result : Byte)() = {
  return 55
}
```

Here `55` is checked against `Byte`, and the caller receives a `Byte`. See
[Zax integer literals and realization](integer-literals.md#concrete-results-and-compile-time-execution).

### Receiver-origin `self` results

`self` on a reference or pointer result is an explicit callable-boundary
contract that every returning path returns exactly the invocation receiver `_`:

```zax
tune final : (
  result self : Engine &
)(profile : Profile readonly &) = {
  apply(_, profile)
  return _
}
```

The compiler verifies the claim. It is not inferred into a public prototype and
does not apply to copied results. The contract lets composition forwarding
preserve receiver provenance without performing a runtime outer cast. Complete
origin, escape, and lifetime meaning belongs to
[lifetimes and references](lifetimes-and-references.md#receiver-origin-self).

### Result mapping and terminal opportunity

A result's declared stance is part of the producer prototype. Omission means
implicit `copy`; caller context does not silently rewrite that contract.

An owned by-value result slot is temporary source storage, however. When that
slot maps into its one complete consumer, selection can determine whether a
`last` offer would materially change the accepted contract:

```zax
consume final : ()(
  input : Item readonly & copy
) = {
}

consume final : ()(
  input : Item mutable writable & last
) = {
}

make final : (
  result : Item
)() = {
  return source
}

consume(make())
// error: state the terminal result intent explicitly
```

This intent error requires:

- an owned by-value result slot;
- implicit result stance;
- structurally final mapping of that slot;
- and a materially different `last`-capable consumer or source outcome.

The producer may declare `result : Item last` or `result : Item copy`. The
caller may write `make() as last`, or acknowledge deliberately retained implicit
behavior with `intent<implicit-stance-at-terminal-use>{...}`.

No error is needed when no `move`/`last` consumer is available, `last` would
accept the same `copy` contract with the same outcome, or the result is discarded.
Each slot of a multiple-result sequence is considered independently.

A reference result is excluded because its slot owns an access path, not the
referred-to value. Destroying the result slot does not establish that the
referent is terminal. Pointer ownership follows the selected pointer contract,
not temporary result-slot lifetime. See
[lifetimes and references](lifetimes-and-references.md#returned-references) and
[pointers and arenas](pointers-and-arenas.md).

### Opt-in result initialization

A result can request construction before body entry:

```zax
make final : (
  result : Item = :
)() = {
  result.name = "example"
}
```

`= :` requests type-default construction. A general initializer directly
constructs from its expression:

```zax
make final : (
  result : Item = makeItem(input)
)(
  input : Input
) = {
  result.finishInitialization()
}
```

After every input and omitted default is complete, opted-in result initializers
run in result-slot declaration order in the selected callee's visible prototype.
They may refer to any completed input parameter.

A raw pointer result may allocate and attach disposition to its result slot:

```zax
makeValue final : (
  result : MyValue * last = @
)() = {
  // result is allocated, constructed, and scheduled before body entry.
}
```

`= @` establishes the pre-body allocation and scheduled raw result.
`last` is the recommended result stance because caller mapping must transfer that
schedule rather than copy only the raw address:

```zax
value : MyValue * = makeValue()
```

The caller declaration adopts the allocation and schedules its disposition. If
the result is discarded, the result slot retains responsibility and dispositions
the successful allocation.

Reporting allocation transfers either a successful schedule or vacancy:

```zax
tryMakeValue final : (
  result : MyValue * last = @!
)() = {
}
```

A scheduled raw result with omitted stance defaults to `copy`, but that default
is suspicious enough to require acknowledgement:

```zax
intent<implicit-stance-at-terminal-use>{
  borrowValue final : (
    result : MyValue * = @
  )() = {
  }
}
```

The ordinary repair is `last`. Explicit `copy` is also legal and states a
deliberate borrowed-result contract:

```zax
borrowValue final : (
  result : MyValue * copy = @
)() = {
}
```

An immediate use may remain inside the producer result slot's lifetime. Retaining
the copied address beyond that schedule requires another proved owner, a
use-site `as last`, or narrow unsafe responsibility where the relationship may be
valid but is opaque. A proved-invalid escape remains an error.

Explicit raw `move` likewise does not transfer the declaration-attached schedule;
it is subject to the same outward lifetime boundary. `deep` is available only
when an exact deep-capable consumer exists and never implies built-in raw-pointee
cloning. Only `last` ordinarily transfers a scheduled raw result's disposition
responsibility.

For ordinary result declarations, non-reporting `@` and `@<` also promise a
non-vacant pointer whenever success is selected. The body may temporarily reset
or replace the result but must restore presence. `@!` and `@!<` permit a vacant
success result.

| Result initializer | Body-entry state | Normal-exit guarantee |
| --- | --- | --- |
| `@` | Definitely present, scheduled | Definitely present |
| `@!` | Possibly vacant, scheduled after success | Possibly vacant |
| `@<` | Definitely present, open-ended | Definitely present |
| `@!<` | Possibly vacant, open-ended after success | Possibly vacant |
| None | Unconstructed result slot | Body must construct one complete pointer value |

An opaque operation may use narrow unsafe responsibility to assert a
presence fact that analysis cannot prove. A path proved to return vacancy
cannot satisfy a non-reporting contract through unsafe assertion.

Current ordinary-result syntax cannot express a delayed-construction contract
that leaves an owning, raw, or optional result unconstructed on body entry while
promising presence whenever success is selected. `= @` provides the promise by
allocating before body entry; an uninitialized pointer or optional result
permits delayed construction but exposes no presence guarantee to callers.

References provide borrowed inputs with no independent vacancy state, but do
not preserve pointer ownership or rebinding. Unchecked pointer dereference may
mechanically bind a reference to Nothing backing; a statically proved vacant
source is diagnosed. General future callable preconditions and postconditions
should express contracts such as “this pointer is present on entry” or “this
optional/owning result is present on normal exit” without inventing
pointer-only type qualifiers.

Pre-body constructedness is part of body/prototype compatibility. A body that
expects a live result on entry cannot be reused with a prototype that leaves
that result unconstructed. A body that expects to construct a result cannot be
paired with a prototype that already constructed it. Compatible prototypes may
change initializer expressions only when they establish the same per-result
entry state required by the body.

A result without an initializer is declared by the prototype but starts empty.
The body constructs into that existing place with `.=`, the same operator a
constructor uses for its members:

```zax
make final : (
  result : Item
)() = {
  result .= [{
    .name = "example"
  }]
}
```

First construction means exactly what the same source would mean as a
declaration initializer:

```zax
result .= source        // like `result : Item = source`
result .= [{ first, second }]
result .= [{}]          // zero-input construction; for an optional, a present payload
```

The expression produces access to the newly constructed result, with the
result's own qualifications; a readonly result produces readonly access.
Explicit construction through `result.+++(source)` remains available and means
the same thing. A [`.=` routing group](#construct-existing-places-with-a-dot-equals-group)
can construct several result slots from one multiple-result producer.

First construction needs no replacement permission, so a `final`, readonly, or
immutable result can be constructed this way. Once the result is live, `.=` is
[reconstructive replacement](construction-and-destruction.md#reconstructive-replacement)
with its usual requirements; a second `.=` on a `final` result is therefore an
error.

The assignment operator `=` never constructs its destination:

```zax
make final : (
  result : Item
)() = {
  result.name = "example" // error: result is not constructed yet
}
```

Construct the result first, or request pre-body construction with `= :` when
every implementation of the prototype should receive a live result. That
initializer is part of the prototype, not a local convenience.

Whether a `.=` constructs or replaces is decided by the conservative rule in
[How the compiler decides first construction](construction-and-destruction.md#how-the-compiler-decides-first-construction).
For result slots, the compiler rejects:

- use before result construction;
- a `.=` or `+++` where the compiler cannot tell whether the result is already
  constructed, such as inside a loop or after paths that disagree; construct it
  outside that region instead;
- a second `+++` on one still-live result slot;
- a value-bearing `return` into a result that is already constructed; and
- normal completion with an incomplete result.

Ordinary `=` never changes into construction according to tracked state. It
requires a live destination and performs ordinary operator selection.

## Return and completion

A value-bearing return supplies the complete ordinary success shape:

```zax
return makeFirst(), makeSecond()
```

Return expressions evaluate left to right and immediately establish their
corresponding result slots.

A bare return supplies no expressions:

```zax
return
```

It is valid only when every ordinary success result slot is already complete.

Falling through the closing brace is equivalent to a bare return:

```zax
make final : (
  result : Item = :
)() = {
  result.name = "example"
}
```

A function uses one complete mechanism on each path:

```zax
make final : (
  result : Item
)() = {
  if condition {
    result.+++(first)
    return
  }

  return second
}
```

Partial value-bearing success-result lists are unavailable. Either:

- the return list supplies the complete ordinary success shape; or
- every named ordinary result slot is already complete and the path uses bare
  return or fallthrough.

Producer `except` is the distinct completion form for one exceptional result:

```zax
read final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  if cannotRead()
    except failure: makeFailure()

  return makeValue()
}
```

It constructs exactly the named exceptional slot, performs ordinary function
exit cleanup, and publishes no ordinary result. Every live provisional result
outside the selected shape must be destroyed by its current owner before
completion. Exact cleanup and handling order is defined by
[Zax exceptional result flow](except.md#exact-exceptional-completion-order).

`return result` is not the completion form for a result slot that already denotes
the output. Use bare return or fallthrough rather than suggesting a transfer from
the slot into itself.

### Position-specific `return #`

`#` in a value-bearing return occupies one result position and means that no new
explicit value is supplied for that slot. For that position it:

1. preserves an already completed result;
2. preserves a result initialized by its declaration;
3. otherwise requests type-default construction; and
4. diagnoses the return if that type cannot be default-constructed.

```zax
foo final : (
  first : Integer = 5,
  second : String
)() = {
  return #, "apple" // preserve 5
}

bar final : (
  first : Integer,
  second : String
)() = {
  first.+++(42)
  return #, "apple" // preserve 42
}

foobar final : (
  first : Integer,
  second : String
)() = {
  return #, "apple" // type-default first
}
```

Because `#` occupies an ordinary success-result position, the complete-shape
rule still holds: a `#` slot is completed, not omitted. Across ordinary result
contexts, `#`
explicitly declines an accessible value or destination at that position while
the surrounding construct determines the lifetime consequence:

- result declarations use `#` to permit caller omission;
- a parameter, local, or other non-result declaration uses `#` so this scope
  may leave that name unread, as taught by
  [declarations and bindings](declarations-and-bindings.md#names-the-body-may-leave-unread);
- ordinary caller mapping uses `#` to consume a produced result without exposing
  a destination;
- a `using` mapping keeps that result as an anonymous owned entry while
  suppressing body access and disposal; and
- return lists use `#` to preserve or default-complete the corresponding slot.

An exceptional outcome cannot be declared or omitted with `#`. The caller
handles or forwards it. A `catch` may still map the payload destination to
`#`, which handles the outcome and introduces no payload name. That form is
taught by [exceptional result flow](except.md#handler-destinations-use-result-mapping).

`return #` differs materially from a bare return: `return #` may default-complete
an unconstructed ordinary slot, while a bare return requires every ordinary
success slot to be complete already.

## Result labels and acknowledgement

Result declarations use ordinary declaration spacing:

```zax
produce final : (
  number : Integer,
  text : String
)() = {
  return 42, "answer"
}
```

At a result-routing site, adjacent `name:` selects the source-facing result
label:

```zax
number:, text: = produce()
```

This shorthand selects results labeled `number` and `text` and introduces
same-named inferred bindings.

This is an intent error:

```zax
number :, text: = produce()
```

The first entry is neither a complete typed declaration nor an adjacent label.

### Required and discardable results

A result marked with `#` may be silently omitted by the caller:

```zax
measure final : (
  requiredValue : Integer,
  diagnostic # : String
)() = {
  return calculateValue(), describeMeasurement()
}
```

The function still completes both slots. The marker changes caller
acknowledgement:

```zax
value := measure() // trailing diagnostic may be omitted
requiredValue: value:, diagnostic: # = measure() // explicit labeled discard
measure() // error: requiredValue was not acknowledged
```

Caller-side `#` explicitly consumes a result without making it accessible to the
caller. It does not destroy that result immediately. The inaccessible source
slot remains part of the complete result consumer and reaches its ordinary
completion position unless a surrounding construct, such as `using`, establishes
an anonymous destination lifetime for it. An unmarked result must be captured,
routed, or explicitly discarded.

A short capture consumes a prefix. Every unmentioned trailing result must permit
omission.

Protected assignment forms such as `a = b` and `place .= source` return access
that is discardable in the same way, so each is a complete statement. Operator
authors choose the same way for their own results; see
[Zax operators](operators.md#discardable-access-from-assignment-forms).

### Exceptional outcome clauses

`catch`, forwarding `except`, and outcome `reshape` attach to the complete
evaluation of the immediately preceding invocation: receiver, explicit
arguments, nested calls, and selected outer callable.

```zax
result := transform(makeValue()) catch failure {
  return
}
```

The handler may receive `failure` from `makeValue` before `transform` starts or
from `transform` after its body runs. Equal-label sources create one
branch-specialized handler mapping. An explicit typed handler destination must
accept every specialization.

```zax
result := transform(
  makeValue() reshape failure: makeFailure:
) catch failure {
  return
} catch makeFailure {
  return
}
```

Clauses run before the invocation's ordinary success results map into an
enclosing call, operator, declaration, packet, `return`, or `using` entry.
Consequently, an exceptional outcome publishes none of those success
destinations.

Handler and forwarding destinations reuse ordinary source-to-destination
mapping:

```zax
call() catch failure: localFailure: {
  return
}

call() except failure: outerFailure:
```

`catch failure` and `except failure` are same-name shorthands. Complete behavior
and the no-discard requirement are taught by
[Zax exceptional result flow](except.md).

## Expression mode and result-mapping mode

Zax distinguishes one expression value from a callable's bare result sequence.

```zax
consume(produce())
```

A bare invocation in a mapping-capable argument-list position may expose several
results to consecutive input parameters.

Grouping requires one expression:

```zax
consume((produce()))
```

This is valid only when `produce()` can form one usable expression value. Several
mandatory results cannot become one value merely through parentheses.

An explicit positional input also expects one expression:

```zax
consume(: produce())
```

Contextual `=` activates result mapping:

```zax
consume(: = produce())
```

It establishes a source-result cursor and a destination-parameter cursor and
maps the producer's bare result sequence.

Parentheses on the mapper's right side force expression mode first:

```zax
consume(: = (produce()))
```

The mapper receives one expression value and cannot recover the parenthesized
call's original multiple-result sequence.

Parentheses are therefore not cosmetic at a multiple-result boundary. A
formatter must not add or remove them as stylistic cleanup.

A `using` resource list is another mapping-capable context:

```zax
using (produceResources()) {
  // Every result is enrolled in declared result order.
}

using ((produceResources())) {
  // The inner grouping requires one expression value.
}
```

Invocation owns the distinction between the bare result sequence and one grouped
expression. [Zax `using`](using.md#multiple-results-and-names) owns which mapped
results are enrolled, named, disposed, or destroyed.

### Operator result shapes

An operator is a callable operation and may declare zero, one, or several result
slots:

```zax
sum:, carry: = customLeft + customRight
```

An operator nested as one expression input must provide exactly one usable value:

```zax
combined := customLeft + customRight
// error if the selected + has two mandatory results
```

The same requirement applies to a word-spelled operand position, and it is what
lets a selected inner result flow outward to the next receiver:

```zax
sum:, carry: = addWithCarry(a, b)

combined := addWithCarry(a, b) next operation
// error: no single intermediate receiver
```

Several operator results do not become an implicit tuple or anonymous structure.
Grouping does not combine them. At a mapping-capable complete producer boundary,
operator results use the same acknowledgement, labels, cursors, construction,
and discard behavior as another callable result shape.

Zero-result operators remain valid complete operations but cannot supply a value
to a parent expression node.

Exact built-in operator result shapes are defined by the
[operator catalog](operator-catalog.md). Operator discovery, candidate-tree
formation, and outward result flow are defined by
[Zax operators](operators.md#outward-result-flow).

## Result-routing groups

An explicit routing group ends with one producer:

```zax
consume(
  number: first:,
  text: second: = produce()
)
```

The producer executes once. The routing entries:

- select `produce.number` and route it to `consume.first`; and
- select `produce.text` and route it to `consume.second`.

The first label in each pair selects the source result. The second selects the
destination parameter. Result routing consistently reads from source to
destination.

`using` resource mapping reuses the same source/destination order, but its
destination label introduces a body-visible resource binding rather than
selecting a predeclared call parameter:

```zax
using (
  resource: myResource:,
  grant: myGrant: = acquirePair()
) {
  use(myResource, myGrant)
}
```

The resource owner requires source-result labels whenever a several-result
producer introduces names. A bare producer may instead enroll every result
without naming it. Complete rules are in
[Zax `using`](using.md#capture-by-source-result-label).

### Two cursors

Routing maintains independent cursors:

- a source-result cursor; and
- a destination-parameter cursor.

A named source resets the source cursor after that result. A named destination
then resets the destination cursor after that parameter.

Named and same-name routing may mix:

```zax
consume(
  number: first:,
  text: = produce()
)
```

`number` routes to `first`. The single `text:` uses same-name shorthand and
routes `produce.text` to `consume.text`.

Same-name routing can use shorthand:

```zax
consume(text:, number: = produce())
```

This routes `produce.text` to `consume.text` and `produce.number` to
`consume.number`.

A bare destination `:` instead supplies the current positional destination:

```zax
consume(
  number: first:,
  text: : = produce()
)
```

Here `text` routes directly to the positional parameter after `first`. Bare `:`
does not introduce a caller-visible name or a separate anonymous intermediate;
the existing parameter slot is the destination.

### Existing destinations and new declarations

Capture may mix a new binding with an existing positional destination:

```zax
existingText : String = "previous"

number:,
text: existingText = produce()
```

`number:` selects and introduces the same-named binding. `text:` selects the
labeled source result and routes it into `existingText` through ordinary
assignment.

These mappings execute in their defined order and are not transactional. A
panic does not roll an earlier effect back or unwind to the caller: a matching
helper repairs the blocked operation and lets it resume, otherwise the process
crashes gracefully. See
[Zax safety and analysis](safety-and-analysis.md#panic-boundary).

### Construct existing places with a dot-equals group

An `=` group assigns to every existing destination. To construct existing
places instead, end the group with `.=`:

```zax
MyType :: type {
  a : A
  b : B

  +++ final : ()() = {
    first: _.a, second: mySecond : MySecond .= makeTwo()
    _.b .= mySecond.makeB()
  }
}
```

- `makeTwo()` runs once.
- `_.a` is an empty member, so `first` constructs it directly. The compiler may
  elide the producer's result slot into the member.
- `mySecond : MySecond` is a new declaration, constructed once from `second`.
- `_.b` is then constructed from `mySecond.makeB()`, and `mySecond` is
  destroyed at the end of the body.

The group's final token decides what happens to every existing destination:

```zax
number:, existingText = produce()
// `=` group: new declarations are constructed; existing destinations are assigned.

first: _.a, second: mySecond .= makeTwo()
// `.=` group: each existing destination receives `.=`.
```

A `.=` group never assigns. Each existing destination receives `.=` exactly as
if written `place .= result`: first construction when the place is empty,
replacement when it is live. When `mySecond` is already live, its entry is
replacement:

```zax
+++ final : ()() = {
  mySecond : MySecond = prepareMeTheSecondPlace()
  first: _.a, second: mySecond .= makeTwo()
  // _.a: first construction, with elision possible.
  // mySecond: replacement from `second`.
  _.b .= mySecond.makeB()
}
```

Replacement cannot elide. A live place cannot become the producer's result
slot, so `second` is an input to the replacement and its temporary is
destroyed afterwards. A `+++ replacement` on `MySecond` that accepts that input
can recycle resources the earlier construction prepared; without one, the
earlier construction only adds a construction and a destruction.

Result slots use the same form, which constructs them without completing the
function:

```zax
make final : (
  a : A,
  b : B
)() = {
  first: a, second: b .= makeTwo()
  finishSetup(a, b)
}
```

Rules:

- The [first-construction rule](construction-and-destruction.md#how-the-compiler-decides-first-construction)
  applies to each entry separately.
- In a constructor, a member used as a `.=` group destination is under
  explicit construction control.
- New declarations may appear and are constructed.
- Entries are processed in order and are not transactional, as in `=` groups.
- Replacement entries follow the ordinary replacement rules: inputs are secured
  before the old lifetime ends, and alias hazards still apply.
- A discarded `#` source result is still constructed by the producer and is
  destroyed when the mapping completes.

A `.=` group is a statement. The other routing contexts have no existing places
to construct into:

| Context | Destinations | Group token |
| --- | --- | --- |
| Call routing | Parameters that the call creates | `=` |
| `using` lists | New resource bindings | `=` |
| Capture producer groups | New captures | `=` |
| `return` routing | Result slots that `return` constructs | `=` |
| Statement routing | New declarations and existing places | `=` or `.=` |

### Typed destinations

A source-result selector may be followed by a complete typed destination
declaration:

```zax
number:,
text: message : String = produce()

consume(number, message)
```

`text:` selects the producer result. That result directly initializes the
caller-owned destination `message : String`; normal construction selects the
conversion or transfer accepted by `String`.

A typed declaration may also occur directly in a call routing group. It supplies
the next positional argument after construction:

```zax
consume(
  number: first:,
  text: message : String = produce()
)
```

This names the first destination parameter explicitly. The new `message`
declaration is initialized from `produce.text` and supplies the next positional
parameter. The source-result selector still appears before the destination
declaration; the ordinary destination cursor identifies the parameter that
receives the constructed intermediate.

Omitting the declaration name creates an anonymous typed intermediate:

```zax
consume(
  number: first:,
  text: : MyTextConsumer = produce()
)
```

`produce.text` initializes the anonymous `MyTextConsumer`. That value then
supplies the current positional parameter and remains alive through its complete
consumer. Unlike bare `:`, the type makes this a destination declaration rather
than a direct positional mapping.

### Anonymous typed declarations

A complete anonymous declaration supplies an explicit expected result type while
forming one expression input:

```zax
anotherFunction(: : Integer = produce())
anotherFunction(: (: Integer = produce()))
anotherFunction((: Integer = produce()))
anotherFunction(# : Integer = produce())
```

These forms differ in how grouping, positional intent, or an anonymous binding
name resolves the surrounding syntax. Once `: Integer` and `# : Integer` have
both resolved as ordinary anonymous declarations in a non-result-mapping
expression, they have the same value behavior. Canonical formatting may prefer
one form later; their parsing roles remain distinct. Complete ordinary
declaration behavior is defined by
[Zax declarations and bindings](declarations-and-bindings.md#anonymous-declarations-and-discard-names).

Anonymous typed construction is required when an expression hole adds an
optional layer around an already-optional source:

Assume `consumeOptional` accepts `Item?` and `consumeNested` accepts `Item? ?`:

```zax
inner : Item?

consumeOptional(inner) // ordinary same-type Item? argument
consumeNested((: Item? ? = [{ inner }]))
consumeNested([{ inner }])
```

The final two calls perform the same deliberate outer wrapping. The explicit
declaration spells `Item? ?`; the short form receives that destination from the
selected `consumeNested` parameter. If callable selection cannot establish one
unambiguous expected type, the bare packet remains invalid.

In the explicit form, "anonymous" describes the declaration's missing binding
name before `: Item? ?`; it does not mean that `Item? ?` is an anonymous
structural type or that the named source `inner` lacks a name.

The packet supplies constructor inputs rather than producing an anonymous
value. Optional depth intent is defined by
[Zax optional values](optional-values.md#adding-optional-depth-requires-a-packet).

### Source and destination discard

Source and destination discard act on different cursors:

```zax
consume(
  #,
  number: first:,
  text: second: = produce()
)
```

The `#` consumes the current producer result without exposing a destination and
advances the source cursor. It does not advance the destination cursor.

A destination discard acts independently:

```zax
consume(
  #:,
  number: second:,
  text: third: = produce()
)
```

`#:` supplies no consumer destination and invalidates the destination cursor. It
does not consume a source result. Later named source and destination labels
reestablish their respective cursors. Source discard does not shorten the result's
complete-consumer lifetime.

Each source result and destination slot may be consumed at most once.

The distinction is observable even when both forms are legal:

```zax
produceText final : (
  text : String
)() = {
  return "produced"
}

consumeText final : ()(
  text : String = "fallback"
) = {
  print(text)
}

consumeText(
  text: : = produceText()
) // produceText.text supplies consumeText.text

consumeText(
  text: # = produceText()
) // discard produceText.text; use the parameter default
```

Bare `:` supplies and advances the current positional destination. `#` supplies
no destination and does not advance that cursor. The second call is valid only
because the unbound parameter can be completed by its default.

Return-result routing uses the same distinction. Bare `:` supplies the current
outer result slot. `#` does not; that outer slot must already be initialized or
be completed elsewhere under the ordinary complete-result-shape rules.

### Several producer groups

Several groups execute in source order:

```zax
consume(
  number: first:,
  text: : = before(),

  third:,
  fourth: = produce(),

  fifth:,
  # = after()
)
```

`before()`, `produce()`, and `after()` each execute once. One group completes and
routes its results before the next producer starts.

Writing `before()` again performs another invocation. Two textual calls never
share one evaluation automatically.

### Cursor exhaustion and optional tails

Simple or explicit mapping consumes producer results while destinations remain.
A discardable result is still mapped when a destination remains; optionality does
not make the mapper skip it to improve a later match.

If the producer has three results and only two destinations remain, the third may
remain unused only when it permits omission.

```zax
consumeFour(
  produceThreeWithLastOptional(),
  produceTwoMore()
)
```

All three results from the first producer map because destinations remain. The
first result from the second producer maps to destination four. Its remaining
result must permit omission. Mapping does not backtrack and discard an earlier
optional result to make room.

### Structural result mapping

Compatibility posture belongs to each producer result declaration. It may make
that result viable for an already known compatible destination at its immediate
mapping boundary. Exact identity remains better; posture does not make result
labels or later uses invent a destination.

A source-result selector may anchor within one result:

```zax
consume(
  packet anchor .payload.point: point:,
  status: = produce()
)
```

The general form is:

```text
<source-result label> anchor <source-path>: <destination>:
```

The path belongs to the selected source result. A declared default result anchor
may supply it. One result slot remains consumable only once, and an anchored
reference cannot escape a by-value result temporary.

`-<` explicitly recomposes remaining result slots into one named or anonymous
value. It is not ordinary grouping and does not change the producer's original
slot identities before the operation.

Transformation belongs to one selected result, never to the complete producer
sequence. A transformed entry preserves ordinary source-to-destination routing
order:

```text
<source-result label>: -<>- [<reshape> -<>-] <destination>
```

For example:

```zax
Point :: type {
  x : I32
  y : I32
}

DevicePoint :: type {
  deviceX : I32
  deviceY : I32
}

PointMapping :: reshape {
  deviceX: x:
  deviceY: y:
}

Size :: type {
  width : I32
  height : I32
}

DeviceSize :: type {
  deviceWidth : I32
  deviceHeight : I32
}

SizeMapping :: reshape {
  deviceWidth: width:
  deviceHeight: height:
}

consumeParts final : ()(
  point : Point,
  size : Size,
  status : Boolean
) = {
}

produceParts final : (
  oldPoint : DevicePoint,
  oldSize : DeviceSize,
  status : Boolean
)() = {
}

consumeParts(
  oldPoint: -<>- PointMapping -<>- point:,
  oldSize: -<>- SizeMapping -<>- size:,
  status: = produceParts()
)
```

The final `= produceParts()` remains the ordinary one-producer routing boundary.
`status` maps directly; only the two entries containing `-<>-` transform.

An explicit leading bare `:` selects the current positional source-result cursor
before the destination declaration:

```zax
: -<>- PointMapping -<>- myPoint : Point,
: -<>- SizeMapping -<>- mySize : Size,
status: = produceParts()
```

A one-result invocation is an ordinary expression source:

```zax
producePoint final : (
  oldPoint : DevicePoint
)() = {
}

myPoint : Point =
  -<>- PointMapping -<>- producePoint()
```

Complete shape, posture, anchor, decomposition, recomposition, transformation,
and reshape semantics are defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

## Forwarding results

### Forwarding into another call

Simple forwarding is positional:

```zax
consume(produce())
```

Result labels do not automatically remap parameter names. Use a routing group to
reorder by labels.

A named argument expects one expression value:

```zax
consume(payload: produceTwo())
```

This is an error when `produceTwo()` cannot form one usable expression value.

### Forwarding from a return

A bare invocation can forward a compatible complete result sequence:

```zax
wrapper final : (
  number : Integer,
  text : String
)() = {
  return produce()
}
```

Return routing can remap labels:

```zax
return text: outputText:, number: outputNumber: = produce()
```

Grouping always requires one expression:

```zax
return (produce())
```

It does not preserve a multi-result sequence.

Result routing itself cannot be grouped as an expression:

```zax
return (
  text: outputText:,
  number: outputNumber: = produce()
)
// error: result routing cannot be grouped as an expression
```

Use naked return routing and explicit continuation for multiline layout.

### Forwarding into construction

A construction packet may forward several results as constructor inputs:

```zax
pair : Pair = [{
  produceTwo()
}]
```

This selects a compatible `Pair` constructor. It does not implicitly pack result
labels into stored members or create one anonymous structural value.

Use explicit `-<` recomposition when the remaining results should become one
structural value; see
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#recompose-several-results-with--).

## Narrow expected-result selection

Result context participates in callable selection only at a direct, complete,
explicitly typed declaration boundary. Applicable contexts include:

- a fully typed local declaration;
- a fully typed declaration expression;
- a complete explicitly typed multiple-result capture;
- a result initializer; and
- a value-bearing return against a complete enclosing result prototype.

```zax
integer : Integer = parseValue(source)
floating : Float = parseValue(source)
byte : Byte = parseValue(source)
text : String = parseValue(source)
```

This allows one discoverable operation family instead of names such as
`parseValueInt`, `parseIntegerValue`, or `intParseValue`.

An inferred declaration supplies no expected result:

```zax
inferred := parseValue(source)
```

A typed declaration can choose the final type of arithmetic performed only on
number literals:

```zax
myByte : U8 = 200 + 55
myBits : U8 = ~1 // error: complement has no width-invariant result
```

The first declaration checks the final mathematical result against `U8`. The
second still fails because unknown positive complement has different signed and
unsigned results; the surrounding declaration cannot reach inside and choose
one interpretation.

See [Zax integer literals and realization](integer-literals.md).

Later use, ordinary assignment, unresolved operators, and outer overloaded calls
do not invent one:

```zax
value = a + parseValue(source)
value := parseValue(source) + parseValue(source)
consume(parseValue(source))
```

The inner call must resolve independently unless source introduces a complete
typed declaration or explicit result-label constraint.

A flow condition is not one of these contexts. A condition validates the result
already selected under ordinary rules rather than supplying an expected
`Boolean`, so an operator whose direct candidates are ambiguous is an error there.
An anonymous typed declaration is the accepted way to supply that context:

```zax
if : Boolean = ?value
  useValue(value)
```

Which operator that selects, and when the opposite operator may supply a
fallback, are owned by
[operators](operators.md#boolean-operations-and-fallback).

### Expected results never choose a source structure

Expected-result context chooses an implementation *within* one already formed
expression tree. It never chooses between distinct operator-phrase extents or
attachment trees:

```zax
result : Desired = source phrase middle phrase tail
// error if two phrase trees remain viable, even when only one returns Desired
```

The allowed direction is `selected inner result -> enclosing receiver selection`,
never `enclosing receiver requirement -> manufacture an inner result choice`. An
inner result-only ambiguity therefore cannot be resolved by an enclosing
operation; the programmer states the intended boundary with a complete typed
declaration. See
[operators](operators.md#candidate-tree-formation-and-selection) for the shared
rule and [operator phrases](operator-phrases.md#results-flow-outward-never-inward)
for its phrase-specific use.

### Same-family matching

Expected-result matching considers:

- result arity;
- base value type;
- qualifications;
- indirection or reference shape; and
- same-family direct transfer viability.

For:

```zax
value : Integer = makeRandom()
```

results declared as `Integer`, a qualified `Integer`, or `Integer &` may be
same-family candidates subject to direct `copy`, `move`, reference, and qualification
rules.

A `Widget` result does not participate merely because an `Integer` constructor
could accept `Widget`. Result selection never searches arbitrary converting
constructors.

Make an intended conversion boundary explicit:

```zax
widget : Widget = (: Integer = makeRandom())
```

The anonymous `Integer` declaration selects the result family. Ordinary `Widget`
construction then consumes that completed value.

### Branch-specific selection under a conditional expression

A conditional expression (`condition ?? c ;; d`) evaluates its condition once and
then only the selected arm. The surrounding operation may resolve a different
callable, overload, or constructor for each arm:

```zax
e := f + (condition ?? c ;; d)
```

This may invoke `f + c` or `f + d`, including different overload bodies, while
preserving strict source evaluation:

1. evaluate `f` exactly once in its original position;
2. evaluate `condition` once;
3. evaluate only `c` or `d`; and
4. invoke the operation selected for that path.

Branch-specific selection does not introduce a second inference model. The narrow
expected-result rule still applies independently on each arm from the same direct,
complete, explicitly typed declaration boundary; the conditional expression does
not propagate an expected result backward from a later statement or outer call.
Each runtime path must converge to one statically usable result shape before the
complete operation completes. The shared condition, single-arm evaluation, and
convergence requirement are owned by
[core flow control](core-flow-control.md#conditional-expression-and-branch-convergence);
this document owns which callable each arm selects.

Final uncommitted integer arm results may realize through the conditional's one
concrete convergence hole. That exception does not propagate through an arm to
discover an inner receiver; see
[integer conditional convergence](integer-literals.md#conditional-convergence).

A path may also select a direct mixfix while another path uses an ordinary
operation:

```zax
result := (condition ?? a * b ;; fallback) + c
```

The true path may select a `*`, `+` mixfix; the false path may select binary `+`.
Common operands retain once-only source evaluation, every path is validated, and
the complete result must converge. Mixfix structural matching, ambiguity, and
decomposition are defined by
[mixfix operators](mixfix-operators.md#branch-specific-mixfix-selection).

An operator node under a conditional expression may also be classified
differently on different paths, so one `&&` or `||` may be a protected
short-circuit operation on one path and an ordinary eager overload on another.
Every path is still validated at compile time. That per-path operator-node
behavior is owned by
[operators](operators.md#branch-specific-selection) and is not restated
here.

## Candidate selection

Callable selection separates:

1. discovery;
2. mapping;
3. viability;
4. preference; and
5. availability.

### Discovery

The callee form determines the initial set:

- a resolved function value supplies one prototype;
- an identifier may name an overload group;
- a member call uses declarations available for its receiver operand; and
- operators use the protected and receiver-owned domains defined by
  [Zax operators](operators.md#discovery).

### Viability

A candidate is viable only when:

- every explicit input and omission maps without duplication;
- every required parameter receives a value or declared default;
- arguments and receiver operands can bind with required types, qualifications,
  and transfer behavior;
- explicit source-result labels exist;
- an applicable expected-result shape is compatible;
- and every required result receives a valid disposition.

Private declarations outside the caller's permitted context are ineligible
before preference. Availability of an eligible visible candidate is checked
after preference so a uniquely best bodyless, forbidden, or unsupported
generated declaration can report unavailable-best rather than falling through.

A number literal can directly supply a parameter declared with an integer type:

```zax
consumeByte final : ()(value : U8) = {
}

consumeByte(55)
```

The compiler checks `55` against `U8`. It does not try conversions or invent
nested temporary values merely to make another overload viable.

### Partial-order preference

Preference is a partial order, not a mismatch score.

For every comparable receiver, input, and applicable result slot, one candidate
may be:

- better;
- equal;
- worse; or
- incomparable.

Candidate A dominates B only when A is no worse everywhere and strictly better
somewhere.

```text
A: exact, conversion
B: conversion, exact
```

Neither dominates the other. The call is ambiguous.

The per-slot better relation must be asymmetric, transitive, and acyclic. It need
not declare every pair comparable.

Transfer fallback compares accepted stance:

- `copy` accepts `copy`;
- `deep` accepts only `deep`;
- `move` prefers `move`, then falls back to `copy`;
- `last` prefers `last`, then falls back to `move` and `copy`.

Value/reference binding, exact qualification, type conversion, receiver stance,
and applicable result quality remain independent dimensions. Better stance does
not automatically defeat worse ownership or qualification behavior.

An exact mutable/writable `move` or `last` reference is better than ordinary
copy-in processing when the source is mutable/writable and every other comparison
is no worse. A readonly reference versus by value, or exact `last` by value
versus fallback `move` by reference, may have no clear overall winner and remain
incomparable.

Receiver stance is another slot:

- exact receiver stance admits only that stance;
- unstanced receiver declaration is fallback for every stance;
- exact receiver stance beats fallback when other comparisons permit dominance;
- receiver matching does not use the source stance fallback ladder.

The remaining complete type, qualification, indirection, generic-specialization,
and generated-candidate preference table is future callable-selection work. Its
eventual rules must fit this partial-order structure.

### Demand-time ambiguity

Distinct declarations remain legal even when no natural call can currently
select one without ambiguity. The compiler does not need a global
declaration-time proof that every callable is naturally selectable.

At a use:

- no viable candidate is an error;
- several nondominated viable candidates are ambiguous;
- an eligible visible declaration may be uniquely best but unavailable to
  invoke, for example because it is bodyless, forbidden, or requires generated
  behavior the language cannot supply;
- that unavailable-best case is an error;
- and selection does not fall through to a weaker available declaration.

A private declaration outside its permitted context is ineligible before this
comparison. It cannot become the unavailable best candidate or block a visible
public declaration.

A resolved function value or exact typed function alias may select one complete
prototype before invocation. Complete exact-prototype syntax and comparison with
future generic specializations remain later callable-selection work.

### Terminal opportunity requires intent

**Terminal** means that analysis can prove this is the source's final ordinary
use before its existing lifetime reaches destruction. It is an opportunity
because a `last` consumer could take ownership of resources that would otherwise
be released soon.

Invocation never silently upgrades implicit `copy` or explicit `move` to
`last`. When analysis proves that an implicit source use is terminal and
offering `last` could materially change the accepted contract, the call is an
[intent error](intent-acknowledgements.md):

```zax
forward(resource)
// error: state copy, move, last, or deep intent explicitly
```

The programmer states the stable choice:

```zax
forward(resource as last)
forward(resource as move)
forward(resource as copy)
forward(resource as deep)
```

No intent error is needed when `last` would accept the same contract and produce
the same source post-state. For example, a copy-only function has no
terminal-aware alternative:

```zax
inspect final : ()(
  value : Record readonly & copy
) = {
}

inspect(record)
```

Decorating this call would not change the selected function or source state. An
explicit declaration stance is not upgraded.

Branches may contain separate path-terminal uses. Loop analysis may recognize
more cases over time. Improved analysis produces new intent errors rather than
silently selecting another user body. Destruction does not count as ordinary
later use.

If a number literal fits several overloads, ordinary source prefers an
`Integer` parameter; source with unsigned intent prefers a `UInteger`
parameter. The compiler chooses that overload before checking the value's
range, and a range failure does not retry another overload. Complete examples
and failures are defined by
[Zax integer literals and realization](integer-literals.md#selection-and-range-order).

### Fewer defaults

After supplied receiver and input bindings compare equally, a candidate requiring
fewer omitted callable-parameter defaults is better.

This comparison counts only parameter defaults activated by omission. It does not
count nested constructor, conversion, member, result, or explicit type-default
work.

Explicit and implicit omission count equally:

```zax
configure(value)
configure(value, option:)
```

when both forms activate the same callable default.

### Expected results as a tie-breaker

Result quality can break a tie only when:

- receiver and supplied-input comparisons are equal;
- a permitted complete declaration supplies the expected shape; and
- the same-family result relation is clear.

A candidate with better inputs wins without trading that advantage against
another candidate's better result, provided its result remains viable.

### Ambiguity and unavailable operations

No tie is resolved by source, declaration, import, module, or generation order.
An equal undominated set is an ambiguity error.

Availability remains separate from match quality. A uniquely best bodyless,
forbidden, unresolved generated-versus-existing, or otherwise unavailable
candidate produces an unavailable-operation diagnostic. Selection does not fall
through silently to a weaker candidate.

Labels, casts, explicit transfers, complete declarations, and exact prototype
bindings provide source-level disambiguation.

## Compatible visible prototypes

### Minted implementation model

A callable declaration with a body is checked and **minted** against its own
implementation prototype:

```text
implementation prototype + body
-> establish body-entry facts
-> select operations inside the body
-> establish internal parameter/result behavior
-> mint one implementation
```

A compatible visible prototype reuses that minted implementation. It does not
reprocess the body, select different internal overloads, change internal
scheduled assignment into open-ended assignment, or remint the callable.

This differs from future generic specialization. A generic body may be
reprocessed for each demanded concrete type, qualification, or other generic
argument. Each resulting concrete specialization is then its own minted
implementation.

An exact prototype can select one member of an overload group:

```zax
selected final : ()(
  value : Document readonly &
) = consume

selected(document)
```

A compatible prototype can also present better labels, defaults, and result
acknowledgement while invoking the same body:

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

Calls through `betterOperation` use `result`, `expires`, its `#` permission, and
`anotherConfiguredTimeout()`.

Compatibility is positional over ordered input and result slots. A visible
prototype supplies a call-boundary contract around the unchanged implementation:

```text
visible inputs/defaults
-> satisfy the minted body's entry requirements
-> invoke the minted body unchanged
-> map its produced results
-> satisfy the visible caller guarantees
```

The new prototype may:

- relabel compatible slots;
- replace call-boundary defaults; and
- change caller result-acknowledgement policy;
- change outward result transfer stance when the minted result can satisfy that
  mapping; and
- expose a scheduled raw result as open-ended, or an open-ended minted result as
  scheduled, while preserving the minted body's internal cleanup behavior.

Relabeling may explicitly rename a corresponding exceptional outcome. The
visible prototype must otherwise preserve each result's
ordinary-versus-exceptional category, the number of exceptional outcomes, their
payload contracts, and body-entry constructedness. It cannot merge several
outcomes under one non-polymorphic result slot.

It may not:

- reorder values;
- convert incompatible values;
- synthesize missing values; or
- enter the body with an unconstructed result when the implementation requires a
  constructed one;
- provide a possibly vacant allocated result to an implementation minted
  under a definitely present result;
- promise callers a definitely present result when the minted implementation
  may produce vacancy; or
- add, remove, discard, or change the category of an exceptional outcome; or
- change operations already selected inside the body.

For allocated raw results, presence and outward cleanup are separate:

```zax
implementation final : (
  result : MyValue * last = @
)() = {
  // Minted scheduled-result behavior.
}

manualResult final : (
  result : MyValue * last = @<
)() = implementation
```

The alias is compatible: both contracts provide and produce a definitely present
pointer. The minted body retains its scheduled replacement behavior, while the
visible boundary leaves final cleanup to `manualResult`'s caller.

`@` and `@!` are incompatible in either direction:

```zax
maybeEntry final : (
  result : MyValue * last = @!
)() = implementation
// error: implementation requires presence on entry
```

Conversely, an implementation minted with `@!` may return vacancy, so an alias
using `@` would overpromise presence to callers.

Changing outward stance does not alter internal stance-driven behavior already
minted into the body. The boundary change is legal only when the produced result
can satisfy it. In particular, exposing a scheduled raw result as explicit
`copy` retains producer cleanup and therefore supports only lifetime-safe
borrowed uses unless a caller restates `last` or proves another owner.

If entering the original body requires executable adaptation, write a wrapper or
lambda.

Composition `via` is different from an ordinary compatible-prototype alias. The
declaration explicitly names a contained target, so its thin adapter may adjust
the receiver to that member and perform the narrowly permitted input mapping:

```zax
start final : ()() = via engine.start
```

Singular `via` must select one source operation and one complete mechanical
mapping. If permitted adaptation leaves several overloads viable, the
declaration is ambiguous rather than choosing by source order or implementation
preference. An exact private helper can make that choice explicit:

```zax
startExact private final : ()(fuel : Fuel readonly &) =
  via engine.start

start final : ()(tank : Tank readonly &) =
  via startExact
```

The helper's visible prototype anchors one overload, then the public declaration
targets that exact helper. This is legal for the same reason a public body may
call an accessible private helper: `start` is an explicit new API declaration,
not publication of `startExact`. It does not let data `via`, automatic `expose`,
or `own` publish a private stored path.

A route that maps a contained reference or pointer result outward must also make
its provenance mechanism explicit:

```zax
// The selected contract proves this result origin.
knownOwner final : (result : Car &)() =
  via engine.selectedPeer

intent<redundant-outer-tracking>{
  possibleOwner final : (result : Car & ?)() =
    tracked via engine.selectedPeer
}
```

Ordinary `via` uses selected-contract proof for the non-optional outer result.
If the programmer deliberately preserves the optional tracked contract despite
that proof, the complete declaration requires
`intent<redundant-outer-tracking>`.

Without selected-contract proof, the checked and asserted forms instead remain:

```zax
possibleOwner final : (result : Car & ?)() =
  tracked via engine.selectedPeer

assertedOwner final : (result : Car &)() =
  unsafe via engine.selectedPeer
```

`tracked via` selects the checked optional mapping and requires the applicable
`outer tracked` capability. `unsafe via` is the programmer's unchecked
non-optional provenance assertion. Compiler-private proof cannot require the
intent acknowledgement or change either portable form. `= existing` cannot
silently discover a mapping that needs tracked outer-result work. Complete
redundancy and cost behavior belongs to
[Zax composition](composition.md#deliberately-retaining-redundant-tracking).

Family forms make a stronger declaration. Here `Engine` supplies two `reset`
overloads:

```zax
Engine :: type {
  reset final : ()() = {
  }

  reset final : ()(profile : Profile readonly &) = {
  }
}

SingleControls :: type {
  engine expose : Engine

  reset final : ()() = existing
}

Controls :: type {
  engine expose : Engine

  reset final : ()() = existing family
}

RenamedControls :: type {
  engine : Engine

  restart final : ()() = via family engine.reset
}
```

`= existing` adopts one uniquely discovered operation. `= existing family`
discovers one whole callable family, while `= via family` explicitly names and
may rename one. The written prototype is an exact family anchor, not a wildcard
or a conversion policy. Every eligible overload must independently obtain one
mechanical outer mapping. Adding an overload that has no unique mapping makes
the outer family declaration fail; the new overload is never silently omitted.

`= forbidden family` is an outer-name fence. It blocks the complete visible
callable name regardless of whether a signature would be direct, exposed, or
adopted from another source family. A later direct declaration with that name
conflicts rather than bypassing the fence.

An ordinary `= implementation` alias does not silently gain those composition
steps. Complete `via` eligibility is defined by
[Zax composition](composition.md#via-selects-one-target).

When the implementation is statically known and the new contract adds no runtime
state, a compiler may direct-call without materializing another function-value
slot. `final` alone is not a universal no-storage guarantee. A closure or
capturing default may still require representation.

This is a programmer-visible compatibility and cost model, not a promise of one
backend mapping or byte-for-byte generated code.

## Temporary lifetime and call completion

The synchronous completion boundary includes:

1. callee and receiver evaluation;
2. explicit input evaluation and immediate binding;
3. omitted-default evaluation and binding;
4. opted-in result initialization;
5. body execution;
6. production of one complete success or exceptional outcome; and
7. mapping of only that selected outcome into the surrounding context.

A temporary bound to a reference parameter survives through that boundary:

```zax
inspect(makeBuffer())
```

If `inspect` receives `Buffer readonly &`, the temporary remains live throughout
`inspect`.

A directly constructed compiler-managed unnamed by-value temporary has
structurally known destruction after its one complete consumer. It inherently
offers `last`:

```zax
result := (: Source).get()
```

If `get` has an exact `last` receiver variant, that variant may be selected.
This is not analysis-dependent upgrading of a named value. A named receiver uses
its declaration stance, and a programmer can restate another stance on the
temporary explicitly.

This does not permit a reference into the temporary to escape:

```zax
view : Buffer readonly & = returnView(makeBuffer())
// error: the reference would outlive the temporary target
```

The reference result retains the temporary's origin, so this stored escape is an
error. A different owning result may explicitly preserve a dynamic life path;
unsafe source cannot make a temporary survive after its path ends.

For nested calls:

```zax
consume(produce())
```

the producer's result slots survive until their transfers or bindings into
`consume` complete. A temporary result bound to an outer reference parameter
survives through the outer call.

### Result slots, destinations, and elision

Result construction before mapping has two owners. The selected visible
prototype determines which result slots are initialized before body entry and
runs those initializers in result declaration order. This includes allocation
forms such as `@`; the caller establishes the call boundary from that prototype.
The implementation controls explicit construction in the body, and
value-bearing return expressions evaluate left to right. A rebound body and
visible prototype must agree on which result slots are already constructed at
body entry. Selecting results in another order does not rewrite any of these
construction effects.

Exceptional result slots are the narrow exception to prototype-controlled
pre-body initialization: they accept no initializer and may be constructed only
when producer `except` or forwarding selects them.

#### Conditional destination publication

Elision may reserve an outer destination's storage through several nested calls
without publishing an instance there. The destination binding and lifetime
become caller-visible only when the selected success or exceptional outcome
contains that result.

If another outcome is selected:

- raw reserved storage needs no destruction;
- a body-constructed nonselected provisional result is destroyed by the callee;
  and
- a prototype-preinitialized nonselected ordinary result is destroyed by
  caller-side invocation machinery.

Successful inner completion transfers provisional cleanup responsibility
outward. An enclosing function that later selects another outcome destroys the
live provisional value even when it occupies its caller's reserved storage.
Complete examples are in
[Zax exceptional result flow](except.md#conditional-result-elision).

#### Caller-visible destination order

Distinct source result slots complete after mapping in reverse result declaration
order. New caller-owned destinations follow the visible order in which the
caller introduces them:

```zax
resultA: firstA:,
resultB: secondB: = makeResults()
// Later destroy secondB, then firstA.

resultB: firstB:,
resultA: secondA: = makeResults()
// Later destroy secondA, then firstB.
```

`makeResults` uses the same prototype-defined pre-body construction and the same
implementation-defined body or return construction in both cases. The caller
has changed only which destination it introduces first, so it has visibly
changed the destinations' later destruction order.

#### Existing destinations keep their order

Routing into an existing destination establishes no new lifetime:

```zax
resultB: existingB,
resultA: existingA = makeResults()
```

`existingA` and `existingB` retain the destruction positions established by
their owning scopes. Likewise, call parameters are destroyed in reverse
parameter declaration order, and result destinations declared by an enclosing
function follow that function's result declaration order. Call-site
source-selection order does not replace either prototype order.

#### Elision moves destruction

Elision may make one source result slot and its destination the same value. The
unified value then has the destination's lifetime and destruction position; it
does not also undergo a separate source-slot destruction. This can change the
overall destruction order.

For example, suppose `makeResults` declares `resultA` before `resultB`. A wrapper
can discard the first result and construct its own empty result slot from the
second with a `.=` group:

```zax
keepSecond final : (
  kept : ResultB
)() = {
  #,
  resultB: kept .= makeResults()
}
```

When `resultB` is elided into `kept`, `resultA` remains a distinct source slot
and is destroyed when the inner mapping completes. The unified `resultB`
survives as `kept` and is destroyed later at the outer destination's position.
The resulting order is `resultA` before `resultB`, rather than the reverse
declaration order that two distinct source slots would have followed.

```text
producer result declarations:  resultA, resultB
elided mapping:                 resultB -> outer destination
inner mapping completion:      destroy resultA
outer destination completion:  destroy unified resultB
```

Elision does not reorder prototype-defined pre-body initialization or the
implementation's explicitly sequenced body and return construction of `resultA`
and `resultB`. It changes lifetime and destruction by removing the separate
intermediate identity and making the destination the produced value. An
implementation may perform this storage unification only with those language
effects.

Transfer stance does not change when ordinary argument or result temporaries
reach destruction. It changes which resources remain owned by those values at
that point. Each temporary is destroyed once at its ordinary completion
boundary.

After body-local scope exit and complete selected-outcome mapping:

1. distinct source result slots are destroyed in reverse result declaration
   order;
2. parameter instances are destroyed in reverse parameter declaration order;
3. caller-side receiver, argument, and nested-result temporaries are destroyed
   in reverse construction order when their complete consumer no longer needs
   them.

An elided result is absent from the first step because its lifetime has become
the destination lifetime. A `#` result remains in that step unless its
surrounding construct explicitly establishes another anonymous destination
lifetime.

This keeps parameters and referenced caller temporaries alive through result
mapping. Returned-reference origin and escape are defined by
[lifetimes and references](lifetimes-and-references.md#returned-references);
pointer ownership is defined by [pointers and arenas](pointers-and-arenas.md).

Async suspension requires a broader completion and lifetime model. It is not
ordinary synchronous completion.

## Multiline return lists

A comma continues an already established comma-list across the following
physical newline:

```zax
return number: first:,
  text: second: = produce()
```

When the first result begins after an otherwise complete bare `return`, explicit
continuation is necessary:

```zax
return \
  number: first:,
  text: second: = produce()
```

The comma already continues the next newline. Adding `\` there is an intent
error:

```zax
return \
  number: first:, \ // error: comma already continues this newline
  text: second: = produce()
```

A continuation-only line can visibly carry the list across another newline:

```zax
return \
  number: first:,
  \
  text: second: = produce()
```

Complete continuation and indentation behavior is defined by
[Zax source structure](source-structure.md).

## Diagnostics

Invocation diagnostics should distinguish:

- no callable found;
- invocation of a provably unavailable function value;
- a type-qualified call to a non-`once` function;
- `?_` in a non-`once` bound function;
- a receiverless type call whose synthesized `copy` receiver cannot select the
  declaration;
- trapping or unprepared Nothing access through `_`;
- unknown parameter or result labels;
- label-versus-declaration intent errors;
- duplicate parameter, source-result, or destination mapping;
- exhausted or invalid positional cursors;
- omitted parameters without declared defaults;
- declared-default omission versus explicit type-default expressions;
- argument, receiver, result, qualification, or transfer mismatch;
- zero results in a value-required context;
- several mandatory results forced into one expression;
- parenthesized expression mode where mapping was intended;
- unconsumed required results;
- an unhandled or multiply handled exceptional outcome;
- exceptional result initialization before producer `except` or forwarding;
- exceptional outcome discard through `#`;
- a handler destination invalid for one nested source specialization;
- incomplete or duplicate result construction;
- a `.=` routing group used in call routing, `using`, capture, or `return`
  routing, where no existing place can receive `.=`;
- an existing destination in an `=` group that has not been constructed yet,
  where a `.=` group would construct it;
- a `return #` slot whose type cannot be default-constructed;
- result context without a complete declaration;
- branch-dependent conditional-expression arms that select incompatible callables
  or fail to converge;
- incomparable undominated candidates;
- a uniquely best but unavailable candidate;
- use after an earlier `move` or `last`;
- a reference escaping a temporary;
- redundant explicit continuation; and
- contradictory continuation indentation.

When candidates map source differently, diagnostics should show the
candidate-specific input and result mapping rather than only list signatures.

When result context participates, diagnostics should show the explicit expected
shape and why each candidate is exact, same-family compatible, incompatible,
unavailable, or incomparable.

Exact identifiers and presentation remain future diagnostics design.

## Costs

Programmers must be able to discover:

- callee and receiver evaluation;
- explicit-input, default, result-initializer, body, return, and routing order;
- hidden declared-default work;
- copies, moves, `last`, references, pointers, and temporary extension;
- indirect function-value calls;
- type-qualified `once` calls without receiver evaluation;
- instance-qualified `once` calls with ordinary once-only receiver evaluation;
- environments retained for closures or default expressions;
- result construction, remapping, omission, and discard;
- selection and mapping of success versus exceptional completion shapes;
- branch-specialized handler mapping for nested equal-label outcomes;
- destruction of nonselected provisional results;
- conditional publication of elided destinations;
- source-slot completion, caller-owned destination order, and any lifetime or
  destruction-order change caused by result elision;
- a `return #` slot preserved, declaration-initialized, or type-default
  constructed;
- branch-specific callable selection under a conditional expression;
- generated or unavailable candidate involvement; and
- future allocation, async, or synchronization machinery used by a call.

Relabeling a statically known compatible function does not inherently require new
runtime machinery. A captured default may add state or work because the
programmer requested that behavior.

## Formatting

Canonical formatting preserves:

- caller source order;
- label adjacency;
- declaration spacing;
- explicit positional intent;
- omission versus type-default expressions;
- parentheses that establish expression mode;
- contextual `=` mapping boundaries;
- source-result selectors before destination selectors;
- bare positional destinations, anonymous typed destinations, and `#` discard;
- `using` resource-list mapping boundaries;
- explicit catch, forwarding, and outcome-reshape clauses;
- explicit transfer intent;
- comma-list continuation; and
- one continuation reason per physical newline.

```zax
send(
  stream,
  encoding: utf8,
  trace: true
)
```

Formatters must not:

- reorder arguments or routing groups;
- replace named mapping with positional mapping;
- add or remove semantically meaningful parentheses;
- convert omission into type-default initialization;
- add or remove transfer semantics; or
- silently repair contradictory continuation intent.

## Source stability

Even deterministic selection cannot prevent every API evolution hazard:

- adding a more specific overload may change the selected body;
- adding a default may make another candidate viable;
- renaming a parameter or result label breaks labeled callers;
- reordering parameters or results changes positional cursors;
- changing result arity or discardability changes mappings;
- adding, removing, renaming, or reclassifying an exceptional outcome changes
  every affected call-site disposition;
- replacing a routed bare positional destination with `#` leaves that
  destination unsupplied;
- reordering explicit destination introductions changes their later destruction
  order without changing prototype- or implementation-defined result
  construction;
- enabling or preventing result elision may move destruction from a source-slot
  completion boundary to the destination lifetime;
- adding a result-only overload may make inferred calls ambiguous;
- adding or changing an overload may change a conditional-expression arm's
  selected callable;
- changing a result type's default-constructibility changes `return #` validity;
- changing transfer or qualification requirements changes viability, cost, and
  post-call source availability;
- adding or removing parentheses can change expression mode versus mapping; and
- adding or removing `once` changes whether a type-qualified call is available
  and whether one declaration is shared across instances.

Possible future mitigations include label aliases, deprecation diagnostics,
contract reflection, and versioned compatibility tooling. Source-order fallback
is not a mitigation.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar,
implementation mapping, compatibility contract, or conformance specification.

Later work must preserve:

- strict caller source order and immediate binding;
- omitted defaults after explicit inputs in selected-callee prototype order;
- visible labels, defaults, and result acknowledgement;
- result slots without mandatory default-construction cost;
- complete ordinary-success or one-exceptional-outcome completion;
- the distinction between one expression and a result sequence;
- explicit result mapping and deterministic cursors;
- visible-prototype-controlled pre-body result construction,
  implementation-controlled body and return construction, caller-visible
  destination ordering, conditional destination publication, and
  elision-driven lifetime unification;
- narrow complete-declaration result selection;
- position-specific `return #` preservation or default-completion within the
  complete-result-shape rule;
- branch-specific callable selection under a conditional expression with
  once-only source evaluation;
- partial-order overload dominance without source-order ties;
- unavailable best candidates not falling through;
- compatible prototype adaptation without executable body adaptation;
- temporary lifetime through synchronous completion; and
- clear programmer-visible costs and diagnostics.

Global and `once` value initialization, concurrent first use, teardown, capture,
and generic-specialization behavior remain future work. They must preserve the
type/instance call distinction established here.

The following remain explicit future work:

- the detailed precedence table for types, qualifications, indirection, `copy`,
  `move`, reference, pointer, and `last`;
- complete closure layout, raw function-pointer ABI, and mutually recursive
  callable-family forwarding;
- generics, concepts, specialization, and generic result deduction;
- variadics, partial application, composition, chaining, and split/combine
  transforms;
- complete ownership, moved-from state, returned aliases, and destruction order;
- async, cancellation, executors, and concurrency;
- runtime value-polymorphic predicates and hidden `mutator` access;
- anonymous/private parameter and result labels;
- label aliases and complete reflection metadata;
- explicit promotion of several results into one structural value;
- formal grammar, diagnostic identifiers, ABI, and conformance; and
- compiler lowering and representation.
