# 036: Generics

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only; closed 2026-11-04 |
| Work Item | `036` |
| Created | 2026-11-02 |
| Owns | The bounded review of the foundation of Zax generics, as defined below |
| Does Not Own | The compile-time execution model, which is current design; async; source generation; the decision itself, which belongs to the language maintainer |

## Non-authority notice

This file is a collaborative working record. Existing documentation, candidate
syntax, examples, and later aligned findings remain non-authoritative until a
separately discussed, aligned, and explicitly authorized promotion incorporates
them into their lasting owners.

## Fixed initiating input

This section records the aligned information known when work item `036` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

What is a Zax generic? In particular: how a declaration takes type parameters
and compile-time value parameters, how a use supplies or infers them, what an
instantiation is and how its identity works, and how constraints decide whether
an instantiation or candidate is valid.

### Motivating pressure

- **Compile-time code is now current.** The compile-time model was settled
  first so that generics could build on it. Generic work must use it, not
  reopen it.
- **Deferred generic input is waiting.** The compile-time review deferred to
  generics:
  - `concept`;
  - `requires` and `true`/`false` candidate enablement after a prototype;
  - SFINAE-style selection;
  - compile-time parameters (the legacy `compile` on inputs);
  - how deeply `wellformed{...}` checks a generic instantiation; and
  - compile points in generic bodies running per instantiation.
- **Current owners already depend on generics.** Examples include the narrow
  `ParameterType : type` slot, type-qualified discovery and alias identity for
  generic types, and literal generic result specialization.
- **Legacy and raw input are large and unreviewed.** They include the legacy
  `$Type` and `Name$(...)` forms, omitted-type inference, computed type
  results, constraints, and bounded size probing.

### Starting input

The maintainer's current thinking is in
[`project/raw/maintainer-notes/generics.md`](../raw/maintainer-notes/generics.md),
which the maintainer fills before this item is assigned. **That document takes
priority over legacy thoughts.** Where legacy material uncovers a hole, the
hole is still reviewed, but against the newest thinking rather than adopted
from legacy.

The maintainer's remaining directive notes,
[`project/raw/maintainer-notes/compiler-directives.md`](../raw/maintainer-notes/compiler-directives.md),
carry direction on compile-time parameters, `requires`, SFINAE, and `concept`.
They also take priority over legacy material.

Legacy pages and raw generic input are evidence. They are not authoritative
over the maintainer's notes.

Before this item was assigned, the maintainer edited the legacy
[`meta-functions.md`](../../meta-functions.md) and
[`meta-types.md`](../../meta-types.md) to bring them closer to the
maintainer's view of generics. Those edits were not a thorough review. Examine
the legacy references in both pages to check that they make sense: that
examples, prose, and routing notes agree with each other, with the
maintainer's generics notes, and with current design. Treat a remaining
inconsistency as a finding to discuss, not as settled design in either
direction.

### Starting boundaries

- Constraints and candidate selection are in scope, because `concept`,
  `requires`, and `wellformed` depth land there.
- Generic partials, generated type families, qualifier-parameterized families,
  and numeric factories are captured for later items rather than designed here,
  unless a concrete consequence makes them necessary.
- Source generation and async remain outside this item.
- The compile-time model in
  [Zax compile-time code](../../language/compile-time-code.md) is current
  design. A conflict with it is recorded as a finding for discussion, not
  resolved by silently changing it.

### Initial stopping guidance

Establish the foundation in order: first the declaration and use model, then
instantiation and identity, then constraints. Give each legacy and raw generic
evidence item a disposition (promote, defer, or reject) rather than designing
every generic feature in full. Expect to work in batches, because generics is
large.

Creating and routing this work item does not authorize analysis. A later
assignment begins it.

Do not promote findings, change current owners, archive this work item, or
create work item `037` without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, promotion, deferral, and closure.
- [Maintainer notes on generics](../raw/maintainer-notes/generics.md) - the
  primary input and the maintainer's current direction; takes priority over
  legacy material.
- [Maintainer notes on compiler directives](../raw/maintainer-notes/compiler-directives.md) -
  the maintainer's remaining direction on compile-time parameters, `requires`,
  SFINAE, and `concept`.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md) -
  preserved generic questions, legacy evidence, compile-time selection pressure,
  bounded probing, and constraints placed on current work.
- Legacy [`meta-functions.md`](../../meta-functions.md) and
  [`meta-types.md`](../../meta-types.md) - the legacy generic model, including
  `$Type`, omitted types, conditional selection, and `final` with
  meta-functions. The maintainer partly updated both pages toward the current
  view without a thorough review, so check their internal consistency as well
  as their content.
- [Zax declarations and bindings](../../language/declarations-and-bindings.md#type-parameter-slots-and-type-arguments),
  type parameter slots and type arguments, and
  [type-qualified operators](../../language/declarations-and-bindings.md#type-qualified-operators) -
  the current narrow type-input baseline.
- [Zax function invocation](../../language/function-invocation.md#type-parameter-slots),
  type parameter slots - how type arguments reach calls today.
- [Zax compile-time code](../../language/compile-time-code.md), especially
  compile-time values, execution policies, compile points, and
  `wellformed{...}` - the model generics must build on.

### Consequence-driven

Read only the smallest relevant sections when the concern reaches them:

- The deferred `compile`, `requires`, and `concept` sections of the legacy root
  [`compiler-directives.md`](../../compiler-directives.md), when compile-time
  parameters or candidate selection are reached.
- [Raw callable selection](../raw/callable-selection.md), when overload
  preference, generated-candidate ambiguity, or callable pre/postconditions are
  reached.
- [Raw reflection](../raw/reflection.md), when instantiation identity, type
  metadata, or specialization identity is reached.
- [Zax literal source and operators](../../language/literal-source-and-operators.md#every-invocation-has-one-concrete-result),
  when uncommitted generic result specialization is reached.
- [Zax identity types](../../language/identity-types.md), when aliases of
  generic types or generic identity are reached.
- [Zax partials](../../language/partials.md), when generic partial visibility
  is reached.
- Raw [mutability-indexed type families](../raw/mutability-indexed-type-families.md)
  and [numeric type families](../raw/numeric-type-families.md), when
  qualifier-parameterized or numeric generic families are reached.

### Audit-only

- Archived numbered work, only when a provenance question cannot be answered
  from current owners or raw input. The compile-time review is
  `035-compile-time-code-and-directives.md`.

## Working record

### Status of this record

The record began as an agent reconstruction of the maintainer's notes, the
legacy pages, and raw input. It was then reviewed in discussion with the
maintainer, and this version records where that discussion landed.

- **Aligned findings** (Part 2) are meanings the maintainer accepted for this
  review. They are still not language design: they become design only through
  a separately authorized promotion.
- **Open items** (Part 3) still need a decision. Each states the candidate on
  the table.
- **Captures** (Part 4) are findings that belong to another owner. Each names
  its destination and what that destination must preserve. They are written
  into those destinations by the promotion change set, not before.
- The pre-promotion documentation fit dry run has **not** been performed. The
  maintainer asked to hold it until these findings are reviewed.

A second review round replaced the ownership rule with open extension and
program-wide agreement (2.18), and settled every item that was then open in
Part 3.

A third round pre-checked the model against every place current owners defer
to or illustrate generics (Part 10). It produced the aligned findings 2.25 to
2.31 and a list of clarifications promotion must make.

Source spellings marked *illustrative* are placeholders for unsettled syntax.
Every other spelling in Part 2 is the form discussed and accepted.

### How to review this record

1. Read Part 1 for the whole model in a few minutes.
2. Read Part 2 section by section. Each section states the rule in plain
   language, shows source, then lists the consequences and edge cases.
3. Part 3 lists what is still undecided.
4. Parts 4 to 9 hold captures, the legacy-page check, evidence dispositions,
   deferrals, superseded ideas, and likely owners. They matter for promotion
   but do not need a first-pass review.

## Part 1: the model in brief

A **generic** is code written once that the compiler processes again for each
set of compile-time inputs it is used with. Each processed result is an
**instantiation**: an ordinary concrete function or type.

There are two ways to give a declaration compile-time inputs.

```zax
// 1. A type passed as an argument. The caller always writes it.
makeDefault final : ()(LiteralType : type) = {
  value : LiteralType
}

makeDefault(Integer)

// 2. A `$` list of compile-time parameters. The caller may write them with
//    `$(...)`, or leave them to be worked out from the arguments.
identity$ final : (UseType : type)(result : UseType)(value : UseType) = {
  return value
}

first := identity$(Integer)(0)   // stated
second := identity(0)            // worked out: UseType is Integer
```

Generic types always state their compile-time arguments:

```zax
MyPair$(First : type, Second : type) :: type {
  first : First
  second : Second
}

myPair : MyPair$(Integer, String)
```

A **constraint** decides whether a set of arguments is acceptable. A short
form names a `Boolean` function in place of `type`. A `requires` clause states
any compile-time `Boolean` condition, including relationships between several
parameters:

```zax
isNumeric final : (result : Boolean)(UseType : type) = {
  return UseType == Integer || UseType == Float
}

MyRange$(Low : isNumeric, High : type) requires canCompare(Low, High) :: type {
  low : Low
  high : High
}
```

A rejected candidate is simply not viable. A generic may have several
declarations (specializations), and any module may add one. The more
specialized declaration wins only when it is clearly more specialized.

An instantiation is formed the first time its arguments are used, from the
declarations visible at that point, and is the same instantiation everywhere
afterward. Every use in the program must make the same choices for it; a later
declaration or another use that would choose differently is an error.

## Part 2: aligned findings

### 2.1 Two forms of generic

Zax has two forms of generic. Both are generics, because the compiler
processes the code once per distinct compile-time input. They differ in how
the caller supplies that input.

**A type passed as an argument.** The parameter's kind is `type`, and it sits
in the ordinary input list. The caller passes the type itself, as an argument,
exactly like any other argument:

```zax
makeDefault final : ()(LiteralType : type) = {
  value : LiteralType
  // ...
}

makeDefault(Integer)

Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
    result .= [{
      .degrees = degrees * 9.0 / 5.0 + 32.0
    }]
  }
}

display := boiling as Fahrenheit   // the right operand is the type argument
```

This is the current narrow type-parameter slot. It is never worked out from
other arguments: the caller always writes the type.

**A `$` list of compile-time parameters.** The declaration's name carries `$`,
and the first list after `:` holds compile-time parameters: types, or
compile-time values. The caller may state them with `$(...)`, or omit them and
let the compiler work them out from the arguments (see 2.9).

```zax
add$ final : (
  ResultType : type = Integer,
  OperandType : type
)(
  result : ResultType
)(
  value1 : OperandType,
  value2 : OperandType
) = {
  return (value1 + value2) as ResultType
}

total := add$(U8)(100, 50)          // ResultType stated; OperandType worked out
other := add$(U8, Integer)(100, 50) // both stated
plain := add(100, 50)               // both worked out or defaulted
```

Positional `$` supply follows the ordinary positional-cursor rules, including
`:` to acknowledge a skipped slot:

```zax
bytes := fillArray$(:, U8)(128)     // first `$` parameter keeps its default
```

Both forms share everything that follows in this part: per-input processing,
identity, constraints, specialization, and lookup.

### 2.2 Generic function values

A generic function has no single runtime function value, because a runtime
call has nowhere to carry a type. Two things are possible instead.

**Satisfying a concrete prototype.** A fully typed function-value declaration
picks one instantiation. The compiler may work out the compile-time parameters
from the destination's prototype, or the source may state them:

```zax
identity$ final : (UseType : type)(result : UseType)(value : UseType) = {
  return value
}

myIdentity : (result : Integer)(value : Integer) = identity          // UseType worked out as Integer
myExplicit : (result : Integer)(value : Integer) = identity$(Integer) // UseType stated
```

`identity$(Integer)` with no argument list names one instantiation as a value.

**Another generic name.** Naming a generic without choosing an instantiation
makes the new name a generic too: a compile-time name for the same family, not
a runtime value. Functions are variables, and the existing `alias variable`
already denotes "the same variable or approved polymorphic variable family", so
no new form is needed:

```zax
myMaker :: alias variable makeDefault   // the whole family, not one instantiation
myMaker(Integer)
```

Writing `myMaker := makeDefault` would suggest a runtime variable, which a
generic cannot have.

Generic *types* are different. A fully applied alias names one concrete type
and works today:

```zax
MyFlags :: alias type MyBits$(Boolean)
```

Naming a generic type family, or applying only some of its parameters, needs a
new declaration shape, because `alias type` preserves a concrete type's
identity and a family is not a type. That is deferred to generic aliases
(Part 7). Bare `MyBits$`, with no parentheses, is reserved to mean "the
family" in that future form. Every instantiation requires `$(...)` (2.4), so
nothing else claims that spelling:

```zax
// Illustrative future shapes:
MyFlagSet$ :: alias type family MyBits$
MyIntMap$(Value : type) :: alias type family Map$(Integer, Value)
```

The same applies to a function with a type argument in its ordinary list
(`makeDefault`) and to a function with `[<constant>]` inputs (2.6): neither
has a runtime function value of its own.

### 2.3 `$` marks a generic; it is not part of the name

`$` on a declaration says "the first list holds compile-time parameters". `$`
at a use says "compile-time arguments are stated here". The name itself is
`add`, not `add$`.

Consequences:

- A generic `bar$` and a non-generic `bar` share one name and one set of
  candidates.
- A call without `$` considers both. A call with `$` considers only generic
  candidates, because only they have a compile-time list to receive the
  stated arguments.

```zax
bar final : (result : Integer)(value : Integer) = { }
bar$ final : (UseType : type)(result : UseType)(value : UseType) = { }

bar(myInteger)            // both are candidates; see 2.17 for which wins
bar$(Integer)(myInteger)  // only the generic is a candidate
```

### 2.4 Generic types always use `$(...)`

A generic type is always written with its argument list, even when every
parameter has a default. There is no inference from a constructor and no bare
form meaning "all defaults".

```zax
MyBuffer$(capacity : Integer = 64) :: type {
  storage : Byte[capacity]
}

small : MyBuffer$(16)
standard : MyBuffer$()     // every parameter defaulted; `()` is still required
```

Why:

- A type's parameters decide its layout and what its members mean. A
  constructor is selected only after the type is known, so inferring the type
  from a constructor argument inverts that order.
- `myValue : MyType = 5` would look like a concrete type while hiding its
  storage. Writing `$()` keeps the cost visible.
- A family name (`MyBuffer`) and one instantiation of it (`MyBuffer$()`) never
  look the same, which matters for aliases, reflection, and diagnostics.

Legacy forms this replaces:

```zax
// Legacy forms, no longer accepted:
myType1 : MyType = 5           // was: parameter inferred from the constructor
myType2 : MyType               // was: all parameters defaulted
MyType.func(5, "hello")        // was: parameter inferred through a `once` call
```

### 2.5 Compile-time value parameters in the `$` list

A `$` parameter may be a compile-time value instead of a type. Its kind is
written like any parameter's type. Because it sits in the `$` list, its
argument must be a compile-time value.

```zax
MyBuffer$(capacity : Integer = 64) :: type {
  storage : Byte[capacity]
}

tiny : MyBuffer$(8)
```

The corrected legacy `fillArray$` shows types and values together. Its input
has its own type, which is converted to the element type:

```zax
fillArray$ final : (
  length : Integer = 10,
  ResultType : type = Integer,
  ValueType : type
)(
  result : ResultType[length]
)(
  value : ValueType
) = {
  myArray : ResultType[length]
  for elem : in myArray {
    elem = value as ResultType
  }
  return myArray
}

result1 := fillArray(5)            // 10 Integer elements; ValueType worked out as Integer
result2 := fillArray$(5)(10)       // 5 Integer elements
result4 := fillArray$(:, U8)(128)  // length defaulted; ResultType stated; ValueType worked out
```

`ValueType` comes last, so stating `$` arguments from the left still works.

Which kinds may be value parameters, and how two value arguments are compared,
is covered in 2.19.

### 2.6 Compile-time inputs in the ordinary input list

Some functions want a compile-time value supplied as an ordinary argument,
with no `$`. The motivating case is a format string checked while compiling:

```zax
formatTime final : (result : String)(
  format [<constant>] : String readonly,
  value : TimePoint
) = {
  if compile !doesThisLookLikeATimeFormat(format) {
    Compilation.error("formatTime: invalid time format")   // illustrative; see Part 4
  }
  // ...
}

text := formatTime("%H:%M", now())    // fine: the format is a compile-time value
other := formatTime(userFormat, now()) // error: userFormat is a runtime value
```

Aligned:

- The `[<constant>]` directive on the parameter marks it. The name matches the
  existing `is constant` query, which asks exactly whether a value is a
  compile-time value. The directive is written after the parameter's name,
  matching where directives follow the name-side words of other declarations.
- The argument must be a compile-time value under the current
  [compile-time value](../../language/compile-time-code.md#compile-time-values)
  rules.
- The marker is part of the prototype. A replacement for a non-`final`
  function must honor it, just as defaults are part of the contract.
- `readonly` is enough to keep the value stable inside the body. `immutable`
  would need `final` so a replacement constructor cannot change it.
- This is not a `$` generic, but it is processed per value (2.7), and the
  function has no runtime function value.

### 2.7 When code is processed once per input

> A function with compile-time inputs (type arguments, `$` parameters, or
> `[<constant>]` inputs) is processed once per distinct set of those inputs.
> Every compile point in its body runs once per processing.

The rule counts inputs, not uses. Compile points can have observable side
effects, so how many times one runs must not depend on whether the compiler
can prove some other compile point ignores an input.

Code without compile-time inputs is processed once, even when its compile
points read globals or call functions with varying results:

```zax
myFunc final : ()() = {
  if compile random() % 50 == 0 {
    Compilation.error("Bad luck there bud!")
  }
}

globalValue : Integer

myFunc2 final : ()() = {
  compile{
    ++globalValue
  }
  if compile globalValue % 50 == 0 {
    Compilation.error("Bad luck there bud!")
  }
}
```

Each function above is processed once, so each compile point runs once,
however many times the function is called. The result may be arbitrary
(`random()`) or depend on what other compile-time work ran first
(`globalValue`), but there is only one version.

A compile-time input is different. The compile-time code depends on a value
that differs between callers, so a single processing cannot serve every call:

```zax
formatTime("%H:%M", now())   // one processing for "%H:%M"
formatTime("%Y", now())      // another for "%Y"
```

Consequences:

- Compile points in a per-input body run once per distinct input, in the
  order the inputs are first used. If they write host-world globals, results
  depend on that order, as for any compile-time code.
- After processing, the compiler may merge versions whose generated code is
  identical. That never erases language-level identity.
- A compile point in a generic body runs in the host world of the module
  instance that defines the generic, not the module that uses it. Current
  design already says a compile-time call into an imported module uses that
  module instance's host world. A counter the generic increments is its own
  module instance's counter, whichever module triggered the instantiation.

### 2.8 Functions may take and return types

A type identity is a compile-time value of kind `type`. A function may take
one (`makeDefault`) and may return one:

```zax
chooseDefaultType final : (result : type)() = {
  return Fahrenheit
}

MySelectedType :: alias type chooseDefaultType()
```

A function that returns a type cannot run at runtime, because a type has no
runtime representation. It behaves as if its execution policy were `compile`.
A type-returning function declared `[<execute=runtime>]` is an error.

A type *input* does not have this effect: `as` takes a type and runs at
runtime.

A type-returning call used as an alias target finishes during compilation and
yields one concrete type, which satisfies the existing "concrete at the alias
boundary" requirement.

### 2.9 How compile-time parameters are filled

Each `$` parameter is filled by the first of these that applies:

1. **Stated** with `$(...)`.
2. **Worked out from arguments.**
3. **Worked out from the expected result**, under the existing narrow
   expected-result rule: only at a fully typed declaration, never through an
   enclosing operator or call.
4. **The default.**

```zax
add$ final : (ResultType : type = Integer, OperandType : type)(result : ResultType)(
  value1 : OperandType,
  value2 : OperandType
) = {
  return (value1 + value2) as ResultType
}

a := add$(U8)(100, 50)        // ResultType stated as U8
b : Short = add(100, 50)      // ResultType from the typed declaration: Short
consume(add(100, 50))         // no typed declaration: ResultType defaults to Integer
```

**Number literals.** Arguments with a known type are used first, and literals
then take the resulting type, exactly as a literal takes a non-generic
parameter's type. Only when every argument for a parameter is a literal does
each literal take its own type, as `value := 0` would. Literals never
coordinate with each other.

```zax
myByte : U8 = 7

c := add(myByte, 1)   // myByte gives OperandType = U8; the literal 1 becomes U8
d := add(1, 2)        // both literals: each becomes Integer; OperandType = Integer
e := add(3, 4.5)      // 3 becomes Integer, 4.5 a real type: no single OperandType, not viable
f := add$(:, Float)(3, 4.5)  // stated: both literals become Float
```

A shared parameter that cannot be worked out to one type makes the candidate
not viable. It is not an error inside the body.

### 2.10 Qualifiers, references, and pointers in worked-out types

A worked-out type keeps the argument's qualifiers, and its reference or pointer
shape. `immutable` can change what is allowed on a value, so dropping it
silently could permit operations that should fail. A reference or pointer
carries through in the same way.

The parameter's own declaration then applies its qualifiers on top, the same
way an alias overlays a pre-qualified type: a qualifier written more locally
replaces an inherited one on the same axis, and omitted axes are inherited.

```zax
show$ final : (UseType : type)()(value : UseType readonly) = { }

myFlag : Boolean immutable = true
show(myFlag)
// UseType is `Boolean immutable`; `value` is `Boolean immutable readonly`
```

Consequences:

- A constraint comparing a worked-out type with `==` sees the qualifiers (2.11).
  A generic that wants to accept several qualifications compares the type with
  its qualifiers removed, using a reflection operation captured in Part 4:

  ```zax
  // Illustrative spelling; the operation belongs to reflection.
  show$ final : (UseType : type)()(value : UseType readonly)
      requires unqualified type of UseType == Boolean = { }
  ```

- Different qualifications of the same argument produce different
  instantiations. `show(myFlag)` and `show(myMutableFlag)` process the body
  twice. This is correct, and promotion should state it as a cost.

**References and pointers.** Because the shape carries, whether a parameter
copies or refers can depend on the argument:

```zax
keep$ final : (UseType : type)()(value : UseType) = {
  myStored := value
}

myFlag : Boolean = true
myRef : Boolean & = myFlag

keep(myFlag)   // UseType is Boolean: value is a copy
keep(myRef)    // UseType is Boolean &: value is a reference, under reference lifetime rules
```

A generic that wants value semantics whatever the argument removes the `&` or
`*` explicitly, through the same reflection stripping used for qualifiers
(Part 4.3). Promotion should teach this case, because a by-value-looking
parameter that becomes a reference is easy to miss.

### 2.11 Comparing types with `==`

`==` on types is exact:

- an alias is equal to its target;
- an identity type is a different type from the type it is based on; and
- qualifiers are part of the comparison: `Boolean immutable == Boolean` is
  `false`.

```zax
MyCount :: alias type Integer
MyId :: identity admit expose type Integer   // an identity type

isSameCount := MyCount == Integer          // true
isSameId := MyId == Integer                // false
isSameFlag := Boolean immutable == Boolean // false
```

Looser comparisons (ignoring qualifiers, or "similar to") belong to
reflection, captured in Part 4.

### 2.12 Constraints: the short form and `requires`

There are two ways to constrain compile-time parameters. Both run during
compilation.

**The short form** names a `Boolean` function in place of `type`. The function
must take exactly one `type` input, so the parameter is always a type
parameter and reads as "a type that satisfies this":

```zax
isTemperature final : (result : Boolean)(UseType : type) = {
  return UseType == Fahrenheit || UseType == Kelvin
}

Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : isTemperature
  ) readonly = {
    // ...
  }
}

display := boiling as Fahrenheit   // accepted
label := boiling as String         // error: no viable `as`; isTemperature(String) is false
```

**A `requires` clause** states any compile-time `Boolean` expression over the
compile-time parameters. It handles relationships between parameters and
value parameters, and it can be as simple as a direct comparison:

```zax
B$(TypeA : type, bValue : Integer = 7) requires useB(TypeA, bValue) :: type {
  a : TypeA
  b : Integer = bValue
}

MyBits$(UseType : type) requires UseType == Boolean :: type {
  packed : Byte[]
}

C$(TypeA : type, TypeB : type, TypeC : type)
    requires isNumeric(TypeA) && isNumeric(TypeB) :: type {
}
```

**Placement.** The clause comes after every parameter it can name:

- on a type, after the `$` list and before `::`;
- on a function, after the complete prototype, among the trailing words
  before `=`, so it can name both `$` parameters and type arguments in the
  ordinary list:

```zax
convert$ final : (
  SourceType : type
)(
  result : DestinationType
)(
  value : SourceType,
  DestinationType : type
) requires canConvert(SourceType, DestinationType) = {
}
```

The clause reaches as far as it can, like `compile`: to the `::` or `=` that
ends it.

**What it may use.** Only compile-time parameters: types, `$` values, type
arguments, and `[<constant>]` inputs. It cannot use runtime inputs, because
it runs before any call happens.

**Combining.** Short-form constraints and a `requires` clause combine with
`&&`. Short forms are checked first, in parameter order, then the clause. The
check stops at the first `false`, so a clause may assume the short forms
passed:

```zax
A$(TypeA : isNumeric, TypeB : type) requires compatible(TypeA, TypeB) :: type {
  // compatible(...) runs only when isNumeric(TypeA) is true
}
```

**Part of the prototype.** A forward declaration or a deferred body repeats the
clause, as it repeats other contract elements.

**Kinds stay visible.** A value parameter always writes its kind
(`bValue : Integer`) and puts any check in `requires`. A reader never has to
open a constraint function to learn whether a parameter is a type or a value.

### 2.13 What a failed constraint does

- A `false` constraint removes that candidate, exactly as a type mismatch does.
- If no candidate remains, the ordinary "no viable candidate" error is
  reported. It names the constraint that rejected each candidate.
- An error inside a generic body is a hard error at that instantiation. It
  never removes a candidate. Only constraints decide viability.

Legacy mechanisms this settles:

- The `[<requires>]` directive is replaced by the `requires` clause.
- A literal `true` or `false` after a prototype, which enabled or removed a
  candidate, is not adopted. `if compile` with `outward` declarations already
  removes declarations, and constraints remove candidates.
- SFINAE, where a body failure silently removes a candidate, is not adopted.
- The `concept` directive is replaced by ordinary `Boolean` functions used as
  short-form constraints or in `requires`.

### 2.14 Checking generic bodies

A `Boolean` constraint says whether arguments are accepted. It does not tell
the compiler which operations the body uses, so a body is checked once per
instantiation. This is an accepted cost.

```zax
supportsAdd final : (result : Boolean)(UseType : type) = {
  return wellformed{ ... }       // checks only `+`
}

addThenMultiply$ final : (UseType : supportsAdd)(result : UseType)(
  value1 : UseType,
  value2 : UseType
) = {
  return (value1 + value2) * value1   // also uses `*`
}

words := addThenMultiply("a", "b")
// error inside addThenMultiply$(String): String has no `*`
//   instantiated from: words := addThenMultiply("a", "b")
```

If the constraint lists every operation the body needs, the error happens at
selection instead. Either way it is an error; only the location differs.

Requirements and techniques:

- The diagnostic shows the instantiation chain as well as the failing
  operation.
- Unused generic code is not fully checked. A library author can test
  instantiations with a compile-time assertion, for example
  `compile{ assert(wellformed{ addThenMultiply(1, 2) }) }`.
- A constraint that checks operations with `wellformed` can declare a lambda
  inside the payload instead of declaring values. Lambda parameters are
  declared, not constructed, so the check does not also require default
  construction. It also states which qualifications the operations must
  accept:

  ```zax
  // Lambda spelling illustrative.
  supportsArithmetic final : (result : Boolean)(UseType : type) = {
    return wellformed{
      check := (result : UseType)(a : UseType readonly &, b : UseType readonly &) = {
        return (a + b) * a
      }
    }
  }
  ```

  Declaring `value1 : UseType` in the payload would also check that `UseType`
  can be default-constructed, which a type supporting `+` and `*` may not be.

`wellformed` and generics: the current owner's wording ("code the payload
creates is checked in full, including a generic instantiation the payload
causes") is sufficient. An instantiation that already exists elsewhere cannot
change the answer: if it compiled, the full check is true anyway; if it
failed, the build has already failed. Instantiations attempted inside a
payload leave nothing behind.

### 2.15 Generic functions are `final`

A generic function must be `final`, for now. Legacy allowed a non-`final`
generic member, with one replaceable slot per instantiation:

```zax
// Legacy behavior, not adopted:
MyType :: type {
  total := 0
  add$ : (UseType : type)()(value : UseType) = {
    total += value as Integer
  }
}
// Each distinct UseType used anywhere would add a slot to MyType, so
// `size of MyType` could not be known until every use in the program was known.
```

A programmer who needs a replaceable slot declares a concrete one for the
instantiation they choose:

```zax
myAdd : (result : Integer)(value1 : Integer, value2 : Integer) = add
```

Relaxing this later would be a compatible change.

### 2.16 Specializations

A generic may have several declarations under one name. Different arguments
may need entirely different implementations, not just mutually exclusive
choices.

```zax
MyBits$(UseType : type) :: type {
  values : UseType[]
}

MyBits$(UseType : type) requires UseType == Boolean :: type {
  packed : Byte[]
}

flags : MyBits$(Boolean)   // the second declaration
counts : MyBits$(U8)       // only the first is viable
```

**Choosing among viable declarations.** Each declaration's constraint is a set
of *terms*: each short-form constraint is one term, and each `&&`-separated
part of its `requires` clause is one term. A part joined with `||`, or wrapped
in a negation, counts as a single term.

> Declaration A beats declaration B when A has every term B has, written
> identically and resolving to the same declarations, plus at least one more.
> Otherwise, if both are viable, the use is ambiguous.

The compiler compares how constraints are written, never what they compute, so
the rule is always decidable. Plain `type` contributes no terms, so any
constraint beats it. C++20 concepts use essentially the same rule.

A refinement wins:

```zax
Fmt$(T : isText, U : type) :: type { }                    // terms: isText(T)
Fmt$(T : isText, U : isNumeric) :: type { }               // terms: isText(T), isNumeric(U)
Fmt$(T : type, U : type) requires isText(T) && isNumeric(U) && isSmall(U) :: type { }
                                                           // terms: isText(T), isNumeric(U), isSmall(U)

a : Fmt$(String, Integer)
// If all three are viable, the third wins: its terms contain each other's terms.
```

Different checks on the same parameter never make a winner:

```zax
Fmt$(T : isString, U : isNumeric) :: type { }   // terms: isString(T), isNumeric(U)
Fmt$(T : isText, U : type) :: type { }          // terms: isText(T)

b : Fmt$(String, Integer)
// error: ambiguous. isString(T) and isText(T) are different terms,
// so neither set contains the other.
```

Writing a shared check identically in both declarations is how an author marks
one as a refinement of the other.

Constraints on different parameters are also not a clear win:

```zax
MyType$(UseTypeA : isString, UseTypeB : type) :: type {
  valueA : UseTypeA
  valueB : UseTypeB
}

MyType$(UseTypeA : type, UseTypeB : isNumeric) :: type {
  valueA : UseTypeA
  valueB : UseTypeB
}

myType1 : MyType$(String, Integer)  // error: ambiguous; {isString(UseTypeA)} and
                                    // {isNumeric(UseTypeB)} do not contain each other
myType2 : MyType$(Integer, String)  // error: neither declaration is viable
```

**Making declarations exclusive.** When two declarations should never both be
viable, one says so:

```zax
isSelectable final : (result : Boolean)(UseType : type) = {
  return size of Target.UseType <= 16     // illustrative layout query; see 2.21
}

MyType$(UseType : isSelectable) :: type { valueA : UseType }
MyType$(UseType : type) requires !isSelectable(UseType) :: type { valueB : UseType }
```

**A specialization for one exact type** is written with an ordinary
constraint, such as `requires UseType == Boolean`. No separate exact form is
introduced now. Adding one later would be compatible, because it would only
resolve cases that are ambiguous today.

Generic function overloads use the same ranking (see 2.17 for how generic and
non-generic candidates compare).

### 2.17 Non-generic and generic candidates

Ordinary preference decides match quality first. Only between equally good
matches does a non-generic candidate beat a generic one. Then specialization
ranking (2.16) applies among generics. Anything still tied is ambiguous.

```zax
bar final : (result : Integer)(value : Integer) = { }
bar$ final : (UseType : type)(result : UseType)(value : UseType) = { }

myInteger : Integer = 3
myByte : U8 = 3

bar(myInteger)   // both exact: the non-generic wins
bar(myByte)      // non-generic needs U8 to Integer; generic is exact with UseType = U8:
                 // the generic wins
```

So adding a convenient non-generic overload does not silently capture calls
that were exact matches for the generic.

### 2.18 Any module may extend a generic; every use must agree

Any module may declare a specialization of a generic, or a helper function a
generic body can find (2.20). That includes a module that owns neither the
generic nor the argument type. Extension is deliberately open: a programmer
introducing or adapting a type must be able to give existing generics a
version that handles it.

```zax
// Module Bits: owns the generic
MyBits$(UseType : type) :: type { values : UseType[] }

// Module Flags: owns MyFlag, imports Bits
MyFlag :: type { ... }
MyBits$(UseType : type) requires UseType == MyFlag :: type { packed : Byte[] }

// Module Tweaks: owns neither MyBits$ nor ThirdPartyType. Also allowed.
MyBits$(UseType : type) requires UseType == ThirdPartyType :: type { packed : Byte[] }
normalize final : ()(value : ThirdPartyType) = { ... }
```

**Each use chooses from what it can see**: the generic's definition, the
modules that own the argument types, and the modules visible where the use is
written, under the ordinary import and export rules. A module that imports
`Tweaks` sees its specialization and its `normalize`.

**Every use in the program must agree.** All uses of the same instantiation,
anywhere in the program, must make the same choices: the same specialization,
and the same bindings for calls that involve a parameter. If two uses would
choose differently, the program is rejected:

```zax
// Module Reports: imports Bits and Vendor (which owns ThirdPartyType), not Tweaks
myData : MyBits$(ThirdPartyType)     // sees only the general declaration

// Module Export: imports Bits, Vendor, and Tweaks
myData : MyBits$(ThirdPartyType)     // sees the specialization from Tweaks
```

```text
error: MyBits$(ThirdPartyType) is chosen differently in two places
  Reports sees only the general MyBits$ (Bits)
  Export sees the specialization declared in Tweaks
  a type has one definition per program; make Tweaks visible to both, or neither
```

Why agreement is required: one instantiation is one type with one layout
(2.19). If uses were allowed to disagree, one type would have two layouts.

The diagnostic is a program-level fact. It names the declaration that makes
the difference and every use that disagrees, so its content does not depend on
which module the compiler reached first. A compiler may report it as soon as
it notices.

**The cost.** Two libraries can each build on their own and fail together.
In the example, `Reports` and `Export` are each fine alone. Combined, they
disagree about `MyBits$(ThirdPartyType)`. This is the price of open extension.
Other languages show the alternatives: C++ allows open extension but leaves a
disagreement silently broken, while Rust forbids extension by a module that
owns neither side. Zax compiles the whole program, so it can allow extension
and diagnose every conflict.

An extender who wants to avoid conflicts entirely can wrap the type in an
identity type of their own and extend that, because no one else can name it.

Within one module, a later declaration that changes an earlier choice is
handled by 2.21.

### 2.19 Instantiation identity

The same generic with the same completed arguments is the same instantiation,
wherever it is written, in every module:

```zax
a : MyPair$(Integer, String)
b : MyPair$(Integer, String)
c : MyPair$(String, Integer)

a = b   // same type
a = c   // error: different instantiations are different types
```

Rules for comparing arguments:

- **Defaults are filled in first.** With `B$(TypeA : type, bValue : Integer = 7)`,
  `B$(String)` and `B$(String, 7)` are the same type.
- **Type arguments compare with exact `==`** (2.11). An alias argument is its
  target; an identity type and a differently qualified type are different
  arguments.
- **Value arguments are the same when every stored part is the same, all the
  way down:**
  - scalars by exact value: integers by value; floating-point including the
    sign of zero and the NaN payload; characters by code point; enums by
    enumerator;
  - strings and arrays element by element;
  - composite types declared stored member by declared stored member,
    including private members. Padding and layout never matter, because they
    are not members;
  - variants by alternative, then value;
  - pointers, references, and capturing lambdas are not allowed as value
    arguments, for the same reason they cannot cross into the program; and
  - unions are not allowed as value arguments, because a union has no defined
    active member to compare.

  A programmer-defined `==` never decides identity, so no user code runs when
  the compiler looks up an instantiation. This is the same member-by-member
  walk that already governs values crossing into the program.

```zax
Scale$(factor : Binary64) :: type { }

x : Scale$(0.1 + 0.2)
y : Scale$(0.3)
// Different types: the two values differ in their last bits. Strange, but defined.

z : Scale$(-0.0)
w : Scale$(0.0)
// Different types: the sign of zero is part of the value.
```

**Identity must agree across modules**, so generic types can appear in module
interfaces:

```zax
// Module Flags
makeFlags final : (result : MyBits$(MyFlag))() = { ... }

// Module App
myFlags : MyBits$(MyFlag) = makeFlags()   // must be the same type and layout
```

**Generative module instances.** Generics behave like every other declaration.
Importing the same source twice creates two module instances with distinct
identities, so `GeometryA.Point` and `GeometryB.Point` are different types.
In the same way, if `GeometryA` and `GeometryB` are two instances of a library
declaring `MyList$`, then `GeometryA.MyList$(Integer)` and
`GeometryB.MyList$(Integer)` are different types:

```zax
first : GeometryA.MyList$(Integer)
second : GeometryB.MyList$(Integer) = first
// error: the generics belong to different module instances
```

Agreement (2.18) applies per generic declaration, so a specialization of
`GeometryA.MyList$` does not touch `GeometryB.MyList$`. Compile points in each
instance's generic bodies run in that instance's host world (2.7).

### 2.20 Name lookup inside a generic body

Principle: **an instantiation's choices must be the same for every use in the
program.** Lookup may differ in what each use can see, but a difference in the
result is an error (2.18), never a silent split.

A generic must pick up helpers declared later for new types, including free
functions and functions from modules that own neither the generic nor the
type. The rule:

1. **Names that do not involve a parameter** resolve where the generic is
   defined, under ordinary source order. `helper(5)` binds once.
2. **Calls whose arguments involve a parameter** also search the modules that
   own the argument types and the modules visible where the use is written,
   including declarations made after the generic.
3. **Ordinary selection** ranks the combined candidates. An exact match for
   the new type beats a library function reached by conversion. Equal
   candidates are ambiguous.

```zax
// Library
normalize final : ()(value : Integer) = { ... }

process$ final : (UseType : type)()(value : UseType) = {
  normalize(value)   // argument involves UseType
}

// Module Readings, written later
MyReading :: type { ... }
normalize final : ()(value : MyReading) = { ... }

// Any module
process(myReading)   // calls the Readings normalize, from every call site
```

Without step 2, `process(myReading)` would either fail or silently call the
library's `normalize(Integer)` through a conversion.

Operations owned by the argument's type, such as a `+` declared on
`MyReading`, are found through the type as usual. Programmers are not required
to add members to make generics work: free functions work too, whether they
are declared in the type's module or in another module visible at the use.

If two uses would bind `normalize(value)` differently for the same
instantiation, because one sees an extra `normalize` and the other does not,
the program is rejected exactly as for differing specializations (2.18).

### 2.21 Forming instantiations and keeping them consistent

> An instantiation is formed the first time its generic and arguments are
> used, from the declarations visible at that point, and is reused everywhere
> afterward. A later declaration, or another use, that would change any of its
> choices is an error.

This section covers changes over time. 2.18 covers disagreement between
modules that see different declarations. Both apply the same requirement: one
instantiation, one set of choices.

"Its choices" are:

- which declaration (specialization) was selected; and
- what each call in its body or in its `requires` clauses bound to when the
  call's arguments involve a parameter (2.20).

```zax
// Module Readings
MyReading :: type { ... }

setup final : ()() = {
  process(myReading)     // forms process$(MyReading): normalize binds to the
}                        // library's normalize(Integer) through a conversion

normalize final : ()(value : MyReading) = { ... }
// error: this declaration changes what `normalize(value)` binds to in
// process$(MyReading), which was already formed above
```

A later declaration can also change a `requires` result:

```zax
// Library
isCompact final : (result : Boolean)(UseType : type) = {
  return false
}

MyStore$(UseType : type) requires isCompact(UseType) :: type { inline : UseType }
MyStore$(UseType : type) :: type { boxed : UseType * strong }

// Module Readings
early : MyStore$(MyReading)    // isCompact(MyReading) is the library's: false;
                               // the general declaration is selected

isCompact final : (result : Boolean)(UseType : type) requires UseType == MyReading = {
  return true
}
// error: this changes what isCompact(UseType) binds to in the `requires` that
// already selected MyStore$(MyReading)
```

**When the error is reported.** By module finalization at the latest. The
compiler may report it earlier, for example when the instantiation is used
again. Checking at finalization matters because the silent case otherwise
returns whenever no later use follows. Zax already has this point: every
`forward` must complete before module finalization.

**Within one module and across modules.** Within a module, the error is
checked by module finalization. Across modules, a later module may see a
declaration that an earlier module did not, and its use would then choose
differently for the same instantiation. That is the program-level
disagreement in 2.18, reported with every disagreeing use and the deciding
declaration. Its content never depends on which module was compiled first.

**`requires` evaluation.** A `requires` clause is evaluated once per argument
set, like a compile point once per processing, and its answer is kept:

- side effects in a `requires` are allowed, and happen exactly once, on every
  compiler;
- the kept answer stays valid only while the choices it depended on hold; a
  later declaration that changes one is the error above, never a silent
  re-evaluation; and
- calls in a `requires` that do not involve a parameter follow ordinary source
  order, like any code.

**Detection is the compiler's business.** The language defines when the error
occurs, not how it is detected. A compiler may re-run selection or record the
choices it made. The rules above ensure neither approach changes what the
program observes: kept `requires` answers are reused, and compile points never
re-run.

Outside this rule, deliberately:

- compile-time state: the `random()` and global examples in 2.7 run once; and
- `wellformed` answers, which already depend on source order, as accepted.

### 2.22 Layout queries in compile-time decisions

Compile-time code runs on the build machine, so an unqualified `size of` there
reports the build machine's layout. When that decides something about the
shipped program, it is almost always wrong:

```zax
isSmall final : (result : Boolean)(UseType : type) = {
  return size of UseType <= size of Integer   // the build machine's sizes
}
```

Aligned:

- **Plain `size of` keeps its current meaning**: the size in the world the
  code is running in. Ordinary code needs that, because memory it allocates
  must use the executing world's layout:

  ```zax
  makeBuffer$ final : (UseType : type)(result : Byte[])(count : Integer) = {
    return allocateBytes(count * size of UseType)   // must be the executing world's size
  }

  myTable := compile makeBuffer$(MyEntry)(16)   // runs on the build machine: host size
  myRuntime := makeBuffer$(MyEntry)(16)         // runs in the program: target size
  ```

- **`target size of` and `host size of`** state the environment explicitly and
  work on any type, including type parameters.
- **An unqualified layout query is an error in a decision position**:
  short-form constraints, `requires` clauses, defaults of compile-time
  parameters, `if compile` and `switch compile` conditions, and anything they
  call. The error is required by necessity: these always run at compile time,
  so the author must say which environment they mean.

  ```text
  error: environment-relative layout query in a compile-time decision
    MyBox$ requires isSmall(UseType); isSmall uses `size of UseType`
    write `target size of UseType` or `host size of UseType`
  ```

- Alignment and byte-order queries follow the same rule.

This does not create an exception to the compile-time rule that compile-time
code uses the build machine's definitions. It forbids the ambiguous spelling
where the difference matters. Details are captured for the `size of` owner in
Part 4.

### 2.23 Runaway instantiation

A generic that instantiates itself with ever-growing arguments never ends:

```zax
deepen$ final : (UseType : type)()(value : UseType) = {
  next : UseType[2]
  deepen(next)   // deepen$(UseType[2]), then deepen$(UseType[2][2]), ...
}
```

Unlike a type containing itself, each step is a new, finite, distinct type, so
only a depth limit can catch it.

- The `[<instantiation-depth=...>]` directive on a generic declaration sets
  the maximum nesting of that same declaration's instantiations:

  ```zax
  deepen$ final [<instantiation-depth=8192>] : (UseType : type)()(value : UseType) = {
  }
  ```
- The compiler-wide default is `4096`. The language guarantees at least that
  depth.
- Exceeding the limit is a diagnostic that shows the growing chain.

### 2.24 Retired forms

- **Omitted parameter types** (`value :`) as a way to make a function generic.
  The `$` list says the same thing visibly, and two spellings for one meaning
  would compete.
- **The `$Type` sigil** inside bodies. Named parameters (`UseType : type`)
  replace it.
- **Constructor and `once` inference of generic types**, and **bare `MyType`**
  for "all defaults" (2.4).
- **Non-`final` generic members** with a slot per instantiation (2.15).
- **The `requires` and `concept` directives**, **`true`/`false` enablement**,
  and **SFINAE** (2.13).
- **Grouping parameters under one constraint** by adjacency, with "longest
  match wins" among constraint overloads. Ordinary selection elsewhere is an
  exact match with ambiguity as an error; this rule would have made parameter
  order and adding a constraint overload change which checks run. `requires`
  replaces it.

### 2.25 Computing types from values during compile-time execution

Types are values at compile time. In code that only ever runs at compile time,
a `$` argument may be any value that exists while that code runs, including a
local computed in a loop.

```zax
selectInteger final : (result : type)(minimumBits : Integer) = {
  width := 8
  while width < minimumBits {
    width *= 2
  }
  return Integer$(LogicalBits: width, Signedness: Sign.Signed)
}

MySelected :: alias type selectInteger(13)   // the instantiation with LogicalBits = 16
```

Rules:

- **Where it applies.** Type-returning functions (2.8), `[<execute=compile>]`
  functions, and `compile{...}` blocks: code that has no runtime form.
- **Where it does not.** In code that may run at runtime (the default `auto`
  policy, `folding`, and `runtime`), `$` arguments must be compile-time values
  known when the body is processed. Otherwise a runtime call could ask for a
  new type.

  ```zax
  makeWidth final : (result : Integer)(bits : Integer) = {
    value : Integer$(LogicalBits: bits)   // error: bits is not a compile-time value here
  }
  ```

- **Formation.** The instantiation is formed when the compile-time execution
  asks for it. It is an ordinary use: identity (2.19), program-wide agreement
  (2.18), and `[<instantiation-depth>]` (2.23) all apply. A failure reports the
  compile-time call chain.

This is what the integer factory, bounded size probing, and payload-dependent
literal suggestions need. Without it, a function evaluated at compile time
could compute a width but could not turn it into a type.

### 2.26 A non-generic type and a generic family may share a name

One non-generic type and a generic type family may share a name. The spellings
never collide:

| Spelling | Meaning |
| --- | --- |
| `Integer` | the non-generic type |
| `Integer$(...)` | an instantiation of the family |
| `Integer$` | the family itself (reserved for generic aliases, 2.2) |

```zax
myCount : Integer                                   // the non-generic type
myOdd : Integer$(LogicalBits: 57, Signedness: Sign.Signed)  // an instantiation
```

- The family may have any number of specializations (2.16).
- There may be at most one non-generic type with that name.
- This matches functions, where a generic and a non-generic of one name already
  coexist (2.3).

Whether `Integer` itself is defined in terms of an `Integer$` instantiation
stays with the numeric owners.

### 2.27 Named `$` arguments use `label:`

Positional, named, and skipped `$` arguments follow exactly the ordinary
argument rules, including the `label:` form for named arguments:

```zax
MyI57 :: alias type Integer$(
  LogicalBits: 57,
  Signedness: Sign.Signed,
  FractionalBits: 0,
  Endianness: Endian.Little
)
```

The current integer owner writes `LogicalBits = 57`. Promotion corrects it, and
the conceptual line `I<W> = Integer$(W, Signed, FractionalBits = 0, ...)`
becomes `I<W> = Integer$(W, Sign.Signed, FractionalBits: 0, ...)`.

### 2.28 Generic operators

An operator declaration may have its own `$` list. `$` attaches to the quoted
operator words, the same way it attaches to a function name:

```zax
MyVector$(Element : type) :: type {
  operator binary '*'$ final : (
    Scalar : isNumeric
  )(
    result : MyVector$(Element)
  )(
    scale : Scalar
  ) readonly = {
  }
}

scaled := myVector * 2.5   // Scalar worked out from the right operand: a real type
```

A phrase operator writes it the same way: `operator post unary 'cluck loudly'$`.

An operator use has no place to state `$` arguments, so operator `$` parameters
are always worked out from the operands, or defaulted. There is no form such
as:

```zax
scaled := myVector '*'$(Float) 2.5   // not a Zax form
```

A programmer who wants to state the instantiation writes an ordinary generic
function and calls it with `$(...)`.

Operators that only use their enclosing generic type's parameters, such as a
`+` inside `MyVector$`, need no `$` of their own. An `uncommitted` literal
result keeps its own marker (Part 10).

### 2.29 Which declarations accept a `$` list

A `$` list is accepted on types, unions, variants, and identity types:

```zax
MyPair$(First : type, Second : type) :: type {
  first : First
  second : Second
}

MyOverlay$(UseType : type) :: union {
  value : UseType
  bits : U64
}

MyResult$(Value : type) :: variant {
  success : Value
  failure : MyError
}

MyHandle$(Underlying : type) :: identity restricted opaque type Underlying
```

and, as already established, on functions (2.1) and operators (2.28).

All four type categories produce one concrete type per instantiation and
already require completion before layout. Generic identity types cover the
identity factories and profile roles that the identity and floating-point
owners expect.

Deferred: generic enums (member generation), generic partials (visibility,
Part 7), and generic aliases (family shapes, 2.2).

### 2.30 Naming the current instantiation inside its body

Inside a generic type's body, the current instantiation is written in full:

```zax
MyVector$(Element : type) :: type {
  operator binary '+' final : (
    result : MyVector$(Element)
  )(
    other : MyVector$(Element) readonly &
  ) readonly = {
  }
}
```

`MyVector$(Element)` is the current instantiation. It anchors type-qualified
operations the way the enclosing name does for a non-generic type. Neither
bare `MyVector` (a possible non-generic type, 2.26) nor bare `MyVector$` (the
family) means the current instantiation.

A programmer who wants a shorter name declares a local alias:

```zax
MyVector$(Element : type) :: type {
  ThisVector :: alias type MyVector$(Element)

  operator binary '+' final : (result : ThisVector)(other : ThisVector readonly &) readonly = {
  }
}
```

Aliases inside type bodies are current design. Aliases inside function bodies
should be supported too, so the same shortening works there:

```zax
process$ final : (Key : type, Value : type)()(input : MyMap$(Key, Value) readonly &) = {
  Entry :: alias type MyMap$(Key, Value) entry type   // illustrative associated type
  first : Entry
}
```

Current owners show `::` definitions in type bodies, namespace bodies, import
injection bodies, and `compile{...}` blocks, but do not state whether they are
allowed in function bodies. Promotion must confirm block-scoped aliases in
[declarations and bindings](../../language/declarations-and-bindings.md#non-value-definitions)
(Part 4.7).

### 2.31 Defaults that depend on other compile-time inputs

A default may use any compile-time input of the same declaration: `$`
parameters, type arguments in the ordinary input list, and `[<constant>]`
inputs. Defaults are evaluated in dependency order; a cycle is an error.

```zax
fillBuffer$ final : (
  Element : type,
  capacity : Integer = 4096 / target size of Element
)(
  result : Element[capacity]
)() = {
}

small := fillBuffer$(U64)()        // capacity = 4096 / 8 = 512
```

- Defaults are decision positions, so 2.22's explicit `target size of` /
  `host size of` rule applies.
- A literal operator's payload is already a compile-time input, because
  literal operators exist only through compile-time literal source. It needs
  no `[<constant>]`. A literal's suggested result can therefore depend on its
  payload:

  ```zax
  operator literal 'h' final : (
    result uncommitted : suggestHexInteger(payload)   // illustrative suggestion
  )(
    payload : String immutable readonly final &
  ) unbound = {
  }
  ```

Allowing references only to earlier parameters would be simpler, but would
block the literal case, where the payload is declared after the result.

## Part 3: still open

Every item previously listed here has been decided:

| Item | Decision | Where |
| --- | --- | --- |
| Compile-time input directive name | `[<constant>]` | 2.6 |
| Instantiation-depth directive name | `[<instantiation-depth=...>]`, default `4096` | 2.23 |
| Reference or pointer argument | `&` and `*` carry through, as qualifiers do | 2.10 |
| Different constraints on one parameter | Term-set rule: identical terms plus more, otherwise ambiguous | 2.16 |
| Generative module instances | Generics belong to their instance; compile points run in the defining instance's host world | 2.7, 2.19 |
| Unions as value arguments | Not allowed | 2.19 |
| Naming a generic family | `alias variable` for functions; generic type aliases deferred, with bare `Name$` reserved | 2.2 |

One minor candidate is not yet decided:

- **A lint for extension by a module that owns neither side.** Candidate: an
  optional, informational lint on a specialization or helper declared by a
  module that owns neither the generic nor the argument type, noting that
  combining it with other code can produce the disagreement error in 2.18. It
  would never restrict extension.

Observation, with no change proposed: a function writes its `$` list inside the
prototype after `:`, while a type attaches it to the name before `::`. A type
definition has no prototype to host the list, so the asymmetry may be
unavoidable, but readers will notice it.

## Part 4: captures for other owners

Each capture is written into its destination by the promotion change set.

### 4.1 Environment-explicit layout queries

- **Destination:** the current `size of` entry in the
  [operator catalog](../../language/operator-catalog.md) and
  [compile-time code](../../language/compile-time-code.md), for the aligned
  rule in 2.22. Residual design goes to
  [raw CPU provider model](../raw/cpu-provider-model.md), whose pressure
  already includes target and compiler-host selection.
- **Must preserve:** plain `size of` reports the active environment;
  `target size of` and `host size of` exist for any type; an unqualified
  environment-relative layout query in a compile-time decision position is an
  error; alignment and byte order follow the same rule; the `isSmall` and
  `makeBuffer$` examples.
- **Still to design there:** exact spellings (an operator form such as
  `target size of UseType` versus qualifying the type as `Target.UseType`), the
  full set of affected queries, and the diagnostic.

### 4.2 Compile-time errors and assertions

- **Destination:** [raw analysis controls](../raw/analysis-controls.md), which
  already holds panic categories and assertions. A `Compilation` member, if
  accepted, would later join the `Compilation` table in compile-time code.
- **Must preserve, as a sketch for future work to settle:**
  - `Compilation.error("message")` reports an error written by the author, at
    the compile point, with the compile-time call chain. The evaluation stops
    and the build fails. Typical use: rejecting an unsupported case in a
    specialization.

    ```zax
    // Illustrative.
    MyBits$(UseType : isPointer) :: type {
      compile Compilation.error("MyBits does not support pointer types; store indices instead")
    }
    ```

  - A separate `Compilation.panic` may be unnecessary: a panic during
    compile-time execution is already a compiler diagnostic. `Compilation.error`
    earns its place by giving the author's message.
  - `Compilation.warning` needs care: `wellformed` treats an unclassified
    language-defined warning as an error, so library warnings must not turn
    callers' `wellformed` checks into errors.
  - `assert(condition, "message")` panics on failure and is governed by the
    existing panic-check controls, so it can be turned off for the shipped
    program.
  - `compile assert(...)` runs the same check while compiling, where failure is
    a diagnostic. Compile-time assertions stay on.

### 4.3 Reflection

- **Destination:** [raw reflection](../raw/reflection.md).
- **Must preserve:**
  - an operation that removes qualifiers from a type, for constraints that
    accept several qualifications. Candidate spelling
    `unqualified type of UseType`; a shorter spelling such as `deduced(...)` is
    a future choice. Include the `show$` example from 2.10;
  - the same stripping for reference and pointer shape, so a generic can
    obtain value semantics when `UseType` was worked out as `Boolean &` or
    `Boolean *`. Include the `keep$` example from 2.10;
  - comparisons looser than exact `==`, such as "similar to";
  - reflecting which declaration (specialization) an instantiation came from,
    and its arguments; and
  - exact `==` on types as the baseline (2.11).

### 4.4 Variable-length compile-time parameter lists

- **Destination:** the variadic-functions entry in
  [raw feature catalog](../raw/feature-catalog.md), with function invocation's
  existing future-work boundary for variadics.
- **Must preserve:** the legacy
  `print final : ()([<compile>] ...) = { }` example, meaning every argument
  must be a compile-time value; and that each argument would follow 2.6 and
  2.7.

### 4.5 Writable compilation settings

- **Destination:** [raw build execution and dependencies](../raw/build-and-dependencies.md).
- **Must preserve:** the maintainer's idea that some `Compilation` settings
  could be writable, raised for instantiation depth and set aside for it. The
  concerns: `Compilation` answers are compile-time values today, so making
  them writable lets answers change between reads; results would depend on
  compile-point order; and one module could change another's settings. For
  instantiation depth, a directive plus build configuration was chosen
  instead.

### 4.6 Callable selection

- **Destination:** function invocation's candidate-selection sections at
  promotion; [raw callable selection](../raw/callable-selection.md) is
  updated for whatever stays deferred.
- **Must preserve:** 2.16 and 2.17 (the term-set specialization ranking, and
  generic versus non-generic only between equal matches); that a failed
  constraint is ordinary non-viability; and the program-wide agreement
  requirement for open extension (2.18).

### 4.7 Aliases inside function bodies

- **Destination:** [declarations and bindings](../../language/declarations-and-bindings.md#non-value-definitions),
  at promotion.
- **Must preserve:** block-scoped `:: alias type` (and the other alias
  categories) inside function bodies, so a generic body can shorten a long
  instantiation name (2.30). If declarations and bindings already intends this,
  promotion states it explicitly; if not, it is a small addition there.

## Part 5: legacy-page consistency check

These pages will be consumed at promotion rather than corrected in place. What
matters is that promotion teaches corrected forms. Each inconsistency is listed
with how it is resolved.

**Resolved by using the newer forms (mechanical):**

- `meta-functions.md`
  - The "labelled argument types" prose still says a type is "prefixed with a
    dollar sign `$` operator", and a comment still names `$Type`.
  - The `compiles` heading sits over a `wellformed` body; the prose has an
    unclosed backtick ("`wellformed block").
  - The `concept` section prose still describes a concept "block" and a
    `(:Boolean)(#:)` calling convention.
  - Two prototypes break `)` and `(` across lines (`next$`, and `add$` in the
    `final` section); prototypes are written `)(`.
  - `MyOtherType.add$` still has `(value:)`; it should be `value : UseType`.
  - The reassignment comment names `add$(U8)(# : Integer, # : Integer)`.
  - `fillArray$(#, U8)` uses `#` where current invocation uses `:`.
  - The forced-arguments example has `(Type)` instead of `(Type : type)`, and a
    comment names `$Type`.
- `meta-types.md`
  - The defaulted-types example keeps `value : $Type`, its comments name
    `$Type`, and it declares `myType2` twice.
  - `MyType$(value : Integer)` has a member also named `value`; the second
    example declares `bits` but uses `myType.value[...]`; comments name
    `$Value` and `$Bits`.
  - The `concept` section heading says "meta-function" on the meta-types page,
    and the example declares `myType1` twice.
- The maintainer's generics notes: `choseDefaultType ... () {` lacks `=`;
  `result1` is declared twice; `knownTemperatureMeasureConcept`'s type
  parameter is lower-case `useType`.

**Resolved by aligned decisions:**

- `fillArray$`'s undeclared `value : Type` meant a second, worked-out type:
  corrected in 2.5.
- The "omitted argument types" section is retired with the omitted form
  (2.24). Its `add(3, 4.5)` comment said the error is inside the body; under
  2.9 the candidate is not viable.
- The constructor-inference and bare-`MyType` sections are retired (2.4).
- The two `MyType$` declarations in the `concept` example become a supported
  specialization, but only with a clear winner (2.16).
- `result2 : Short = add(100, 50)` is supported under the narrow
  expected-result rule (2.9).
- The non-`final` generic sections are retired (2.15). The `alternativeAdd`
  capture placement belongs to lambda syntax and is not carried forward.

## Part 6: evidence dispositions

| Evidence | Disposition |
| --- | --- |
| Type parameter in the ordinary input list (`as`, `makeDefault`) | Promote as one of two generic forms (2.1) |
| `add$` leading compile-time list on functions | Promote (2.1) |
| `Name$(...)` generic types | Promote, always with `$(...)` (2.4) |
| `$Type` sigil | Superseded (2.24) |
| Omitted parameter types (`value :`) | Retired (2.24) |
| Concept functions in the parameter position | Promote as the short form, restricted to one `type` input (2.12) |
| Grouping parameters under one constraint | Rejected (2.24) |
| `requires` | Promote as a clause, not a directive (2.12) |
| Type-returning functions and computed defaults | Promote (2.8) |
| Compile-time value parameters in the `$` list | Promote (2.5) |
| `compile` on ordinary inputs | Promote as the `[<constant>]` directive (2.6) |
| Constructor and `once` inference of generic types | Rejected (2.4) |
| Overloaded generic type names (legacy `concept` example) | Promote as specialization (2.16) |
| Body errors are hard errors | Promote (2.13) |
| `true`/`false` enablement; SFINAE | Rejected (2.13) |
| `concept` directive | Superseded (2.13) |
| Dual-mode `size of` in constraints | Superseded by explicit layout queries (2.22) |
| Non-`final` generic member slots | Rejected (2.15) |
| Expected-result inference (`result2 : Short = add(...)`) | Promote under the narrow rule (2.9) |
| `[<compile>]` on variadic inputs | Captured (4.4) |

## Part 7: deferred adjacent findings

Each already has a home in
[raw type parameters and generics](../raw/type-parameters-and-generics.md)
unless stated. Listed with the constraint this item places on it:

- **Generic partials, and partial grants seen by generic bodies.** Must follow
  2.18 and 2.20: a partial granted where a use is written may contribute to
  what that use sees, but every use of the same instantiation must still make
  the same choices, or the program is rejected. Reactivates when
  partial-added operations must work through library generics.
- **Qualifier and stance families.** Every instantiation is one exact
  prototype. Worked-out types keep qualifiers (2.10); explicit qualifier
  parameters remain future work.
- **Numeric factories; intrinsic integer, fixed, floating, and endian
  families.** Depend on type-returning functions (2.8) and value parameters
  (2.5), and on computing instantiations during compile-time execution
  (2.25). Named `$` supply uses the ordinary label form,
  `Integer$(LogicalBits: 57, ...)` (2.27).
- **Cursor-protocol and allocation-policy constraints.** Will test how
  constraints compose by capability.
- **Nothing policy per specialization; type, union, and variant completion.**
  Each instantiation is complete before layout; 2.15 removed the first known
  conflict.
- **Generic aliases and forwards.** A fully applied alias names one
  instantiation. A generic type family alias, a partially applied alias, and
  a forward for a generic family need their own shapes. Bare `Name$` without
  parentheses is reserved to name the family (2.2). Generic function families
  already use `alias variable`.
- **Optional depth through substitution.** Substitution never flattens `?`
  layers.
- **Literal `uncommitted` results.** Should be explainable as ordinary
  compile-time parameter selection once this foundation is promoted.
- **Source generation.** Remains in the legacy compiler-directives page.

## Part 8: superseded candidates from the initial reconstruction

Recorded so promotion does not reintroduce them:

- **"One mechanism, two supply routes."** Replaced by "two forms of generic,
  sharing one instantiation model" (2.1). Passing a type and declaring `$`
  parameters are distinct forms.
- **Deduction drops qualifiers.** Replaced by exact worked-out types with
  explicit stripping (2.10).
- **Only uses of a compile-time input force per-value processing.** Replaced
  by counting inputs (2.7).
- **Constraints see target layout by default.** Rejected as an exception to
  the compile-time rule; replaced by the decision-position error (2.22).
- **Positional specialization rule** (all specializations before a module's
  first use of the generic). Rejected: it blocked natural ordering within a
  module. Replaced by 2.21.
- **Memoization per module.** Rejected: identity must agree across modules
  (2.19).
- **Definition-site lookup only.** Rejected: it could not find helpers for new
  types (2.20).
- **The ownership rule** (only the generic's owner or the argument type's
  owner may specialize or supply helpers). Rejected: other modules must be
  able to extend specializations. Replaced by open extension with program-wide
  agreement (2.18).
- **"An instantiation depends only on the generic and its arguments."**
  Replaced by "an instantiation's choices must be the same for every use"
  (2.20), which permits lookup in a use's visible modules.
- **Per-parameter specialization ranking.** Replaced by the term-set rule
  (2.16).
- **Deduction drops reference or pointer shape.** Replaced by carrying `&` and
  `*` through, with explicit stripping (2.10).
- **`myMaker := makeDefault` for naming a generic.** Replaced by
  `alias variable` (2.2).
- **Value identity by programmer-defined `==`.** Replaced by structural
  identity (2.19).
- **Writable `Compilation` settings for instantiation depth.** Replaced by a
  directive (2.23); the idea is captured (4.5).

## Part 9: likely documentation owners and current-owner consequences

Candidate only, for the dry run to test:

- **A new current owner for generics**: both forms, `$` lists, compile-time
  value and `[<constant>]` inputs, per-input processing, filling parameters,
  constraints and `requires`, specialization and open extension, lookup,
  formation and program-wide agreement, identity, runaway limits, costs, and
  diagnostics.
- **[Namespaces and modules](../../language/namespaces-and-modules.md):**
  possibly a short note that generics belong to their generative module
  instance like any other declaration (2.19), if the generics owner alone does
  not make that discoverable.
- **[Declarations and bindings](../../language/declarations-and-bindings.md#type-parameter-slots-and-type-arguments)
  and [function invocation](../../language/function-invocation.md#type-parameter-slots):**
  keep the local rule for type arguments and hand off to the generics owner.
  Invocation's candidate selection gains the generic-versus-non-generic rule
  and specialization ranking.
- **[Conversions and casts](../../language/casting.md#writing-your-own-as):**
  replace "a type parameter slot cannot yet restrict which types a caller may
  name" with a constrained `as`.
- **[Compile-time code](../../language/compile-time-code.md):** the
  decision-position layout error; per-input compile points; `Compilation.error`
  if accepted; boundaries updated now that compile-time parameters are
  designed. The existing `wellformed` depth wording stays.
- **[Operator catalog](../../language/operator-catalog.md):** `target size of`
  and `host size of`, if their spellings are settled at promotion.
- **Legacy `meta-functions.md`, `meta-types.md`, and the deferred sections of
  `compiler-directives.md`:** consumed, then retired or reduced.
- **Raw type parameters and generics, and the maintainer notes:** shrink to
  what remains deferred, or retire.
- **Owners touched by the pre-check (Part 10):** integers, fixed-point and
  floating-point scalars, endianness, strings and characters, literal source
  and operators, integer literals, identity types, type definitions, variants,
  and terms. Each replaces hand-waving with the settled form, or states the
  clarification listed in Part 10.

## Part 10: pre-check against current owners

Before the dry run, the maintainer asked for a check that every place current
owners defer to generics, or illustrate them with placeholder syntax, can be
resolved by this model without causing problems. Every current `language/`
mention of generics was reviewed. Mentions where "generic" only means
"general" (for example "generic transfer-stance source forms") were skipped.

### 10.1 Resolves cleanly

| Current owner and passage | Resolution |
| --- | --- |
| [Conversions and casts](../../language/casting.md#writing-your-own-as): "a type parameter slot cannot yet restrict which types a caller may name" | A constrained `as` (2.12). This also settles the raw concern that a partial adding `as` to `I32` would compete with protected conversions such as `myCount as I64`: a constrained destination never matches `I64` |
| [Type definitions](../../language/type-definitions.md) and [variants](../../language/variants.md): an instantiation must be one complete concrete type before layout, lifecycle, and coverage | Matches 2.19. Delayed layout from partials already spreads to instantiations; a `requires` asking the size of such a type waits, as `size of` already does |
| [Nothing instances](../../language/nothing-instances.md): forming an unused specialization needs no backing | Unchanged |
| [Function invocation](../../language/function-invocation.md): preference is a partial order that must be asymmetric, transitive, and acyclic | The term-set rule (strict superset) and "non-generic only between equally good matches" both fit (2.16, 2.17) |
| [Function invocation](../../language/function-invocation.md) and [composition](../../language/composition.md): preferred projection "does not invent a target for generic deduction" | A parameter is worked out from the argument's own type, never through a `preferred` member (2.9) |
| [Transfer stances](../../language/transfer-stances.md) and [qualifiers](../../language/qualifiers.md): omission never makes a declaration generic over qualifiers; a generic result resolves to one prototype | Matches 2.10 and 2.15 |
| [Integers](../../language/integers.md) and [fixed-point scalars](../../language/fixed-point-scalars.md): associated types (`MyInteger bit count type`) and the full-precision product result | Type-returning operations are compile-only (2.8). Inside a generic they are found through the argument's type (2.20) and may appear in prototypes, such as `result : UseType bit count type` |
| [Iteration](../../language/iteration.md) and [enums](../../language/enums.md): constraints for an unknown cursor or iterable | `requires` with the `wellformed` lambda technique (2.14) can express each protocol piece. Verbose but not blocked |
| [Partials](../../language/partials.md): which partials a generic body sees | Bounded by 2.18 and 2.20; generic partials stay deferred (Part 7) |

### 10.2 Gaps found, now decided

Each became an aligned finding in Part 2:

- **Computing a type from values during compile-time execution** (2.25). The
  integer factory, size probing, and payload-dependent literal suggestions
  needed it.
- **`Integer` and `Integer$` sharing a name** (2.26). The integer owner already
  uses both.
- **Named `$` arguments** use `label:`, not `=` (2.27).
- **`$` lists on operators** (2.28).
- **Which declarations accept `$`** (2.29). Identity factories, profile roles,
  and generic variants needed more than `:: type`.
- **Naming the current instantiation** (2.30), with function-body aliases
  captured (4.7).
- **Defaults depending on other compile-time inputs** (2.31). The literal
  suggestion and `fillArray$` needed it.

### 10.3 Clarifications promotion must make

These are consistent with the model but need stating, or the current wording
would mislead.

**Host and target builds.** The floating-point owner says shared code "may be
specialized for compiler-host and target execution". An environment-relative
type such as `Integer` or `U32` has one identity with a different definition
in each build, and the same holds for instantiations over it:

```zax
MyTable$(Entry : type) :: type { entries : Entry[16] }

compile{
  hostTable : MyTable$(Integer)   // processed for the build machine
}
targetTable : MyTable$(Integer)   // processed for the target: same identity, possibly different layout
```

So 2.19's "one instantiation, one layout" should read "one identity and one
set of choices; one layout per build". Choices are the same across builds
because decision positions must use explicit `target size of` or
`host size of` (2.22).

**Intrinsic families normalize their arguments.** Some intrinsic argument sets
denote the same type:

- a "native" or "agnostic" endianness selector resolves to `Little` or `Big`
  before the type exists ([endianness](../../language/endianness.md));
- a fixed-point specialization with `F = 0` is the integer specialization.

These are language-defined normalizations applied before identity comparison,
owned by the numeric owners. Programmer-declared generics compare arguments as
written (2.19).

**`uncommitted` literal results fill candidate holes.** A literal result may
take its type from a typed declaration, an assignment, a parameter, or a result
slot ([integer literals](../../language/integer-literals.md)):

```zax
consumeByte final : ()(value : U8) = { }
consumeByte(h'FF')   // the literal's result specializes to U8 from the parameter
```

That is broader than 2.9's rule for ordinary generic results, which takes an
expected result only from a fully typed declaration. It is consistent because
`uncommitted` is a literal-only opt-in whose result behaves like number-literal
source. Promotion states that distinction.

**Angle-bracket notation becomes `$(...)`.** Current owners write conceptual
generic applications with angle brackets:

```text
MbcsString<Encoding>                  ->  MbcsString$(Encoding)
Legacy.TerminatedCharString<Policy>   ->  Legacy.TerminatedCharString$(Policy)
FastF<N>                              ->  FastF$(N)
```

`I<W>` and `I<W>F<F>` stay as they are: they describe naming patterns such as
`I16F8`, not source syntax. A terminator policy is best a type argument,
because its behavior (terminator, rejection, restoration after mutation) is
found through operations owned by that type (2.20).

**Overlay never grants capability.** 2.10's overlay changes the requested
profile but, as with aliases, never grants authority the source lacks:

```zax
hold$ final : (UseType : type)()(value : UseType) = {
  item varying : UseType = value
}

myFixed : Foo final = makeFoo()
hold(myFixed)   // UseType is `Foo final`: `item varying` is a capability error at instantiation
```

**Specializations may differ in members.** If `MyBits$(Boolean)` stores
`packed` instead of `values`, generic code using `.values` fails for `Boolean`
at instantiation:

```zax
firstOf$ final : (UseType : type)(result : UseType)(bits : MyBits$(UseType) readonly &) = {
  return bits.values[0]
}

firstOf(myFlags)   // error inside firstOf$(Boolean): MyBits$(Boolean) has no `values`
```

That is correct under 2.13, but it is the well-known C++ `vector<bool>`
pitfall. Promotion teaches it with one example and suggests keeping a common
surface across specializations.

**Compile-time values.** [Compile-time code](../../language/compile-time-code.md#compile-time-values)
gains `$` value parameters and `[<constant>]` inputs in its list of
compile-time values; both satisfy `is constant`.

**Identity-type spelling.** Generic identity types use the existing identity
declaration form (`MyHandle$(Underlying : type) :: identity restricted opaque type Underlying`),
not a bare `:: Underlying`.

**Terms.** The terms reference gains generic, instantiation, specialization,
constraint, `requires`, compile-time input, and worked-out (deduced) parameter.
Its existing "uncommitted generic result" entry stays, linked to the
`uncommitted` clarification above.

### 10.4 Not affected

- The raw cursor-protocol, allocation-policy, string-policy, and Nothing-policy
  constraints (Part 7) remain deferred; nothing in the pre-check conflicts with
  them.
- The raw whole-type contract pressure remains deferred. A future contract
  facility could appear as another `requires` term without changing the
  term-set ranking.

## Part 11: pre-promotion documentation fit dry run

Performed 2026-11-03 at the maintainer's request. Read-only for every file
except this record. It authorizes nothing.

### 11.1 Result: PASS

Every aligned finding in Part 2 maps to one lasting owner. One new current
owner, `language/generics.md`, is needed and can teach the concept cohesively.
The adjacent owners can absorb their local rules and handoffs without
competing definitions. Every deferred item has a live raw destination. Both
legacy generic pages can retire, because all of their useful input is
promoted, rejected, or deferred.

The result depends on four points the maintainer should confirm when
discussing the change set (11.9). None is a design gap:

1. one new owner rather than several (11.3);
2. how C++ and Rust contrasts are presented (11.4);
3. the open extension lint going to raw input rather than being decided now;
4. the size of the change set (about 35 files), applied as one coherent change
   in two passes (11.8).

### 11.2 Ownership map

Each aligned finding, its lasting owner, and the local handoffs elsewhere. A
section name in `generics.md` refers to the outline in 11.4.

| Finding | Lasting owner | Local handoffs |
| --- | --- | --- |
| 2.1 Two forms of generic | `generics.md` §2 | Declarations and bindings and function invocation keep the type-parameter-slot rule and link here |
| 2.2 Generic function values; naming families | `generics.md` §5 | Declarations and bindings: `alias variable` already denotes a polymorphic family; add one sentence that a generic function family qualifies |
| 2.3 `$` is not part of the name | `generics.md` §3 | Function invocation: candidate discovery notes that `name` and `name$` are one set |
| 2.4 Generic types always use `$(...)` | `generics.md` §4 | None |
| 2.5 Compile-time value parameters | `generics.md` §2 | None |
| 2.6 `[<constant>]` inputs | `generics.md` §2 | Compiler directives: catalog entry. Compile-time code: compile-time values list |
| 2.7 Per-input processing | `generics.md` §13 | Compile-time code: "When compile points run" gains one paragraph and links here |
| 2.8 Types as compile-time values; type-returning functions | `generics.md` §7 | Compile-time code: execution-policy table notes that a type-returning function has no runtime form |
| 2.9 Filling parameters | `generics.md` §3 | Function invocation: narrow expected-result selection notes that it may also work out a generic parameter |
| 2.10 Qualifiers, references, pointers carry | `generics.md` §3 | Qualifiers: replace "complete generic result resolution remains later function design" with a link |
| 2.11 Type `==` | `generics.md` §6 | Identity types: one sentence linking aliases and identities to type comparison |
| 2.12 Constraints and `requires` | `generics.md` §8 | Conversions and casts: constrained `as` |
| 2.13 Failed constraints; no SFINAE | `generics.md` §8 | Function invocation: viability notes that a failed constraint is non-viability |
| 2.14 Checking generic bodies; `wellformed` | `generics.md` §9 | Compile-time code: replace "the details belong to future generic design" with a link |
| 2.15 Generics are `final` | `generics.md` §3 | None |
| 2.16 Specializations and term-set ranking | `generics.md` §10 | Function invocation: partial-order preference replaces "generic-specialization ... is future callable-selection work" with a link |
| 2.17 Non-generic between equal matches | `generics.md` §10 | Function invocation, as above |
| 2.18 Open extension and agreement | `generics.md` §12 | None |
| 2.19 Identity, value arguments, module instances | `generics.md` §6 | Namespaces and modules: one sentence that generics belong to their module instance |
| 2.20 Lookup inside generic bodies | `generics.md` §11 | Partials: boundary question answered by link; generic partials stay future |
| 2.21 Formation and consistency | `generics.md` §12 | None |
| 2.22 Layout queries in decisions | Compile-time code (new section) | Operator catalog: `target size of` and `host size of` rows. `generics.md` §8 gives a local example and links |
| 2.23 Runaway instantiation | `generics.md` §14 | Compiler directives: catalog entry |
| 2.24 Retired forms | Not taught as history. Each retirement is reflected by teaching the valid form; see 11.4 | None |
| 2.25 Types from values during compile-time execution | `generics.md` §7 | Integers: the factory becomes expressible (API still future) |
| 2.26 Shared name with a non-generic type | `generics.md` §4 | Declarations and bindings: "Identifier namespace and paths" adds this as a structured case |
| 2.27 Named `$` arguments | `generics.md` §3 | Integers: correct `=` to `:` |
| 2.28 Generic operators | `generics.md` §5 | Operators: declaration forms mention `'*'$` and link |
| 2.29 Declarations accepting `$` | `generics.md` §4 | Type definitions, variants, identity types: replace "exact generic syntax remains future" with a link. Unions: add one linking sentence |
| 2.30 Current instantiation; local aliases | `generics.md` §4 | Declarations and bindings: function-body aliases (4.7); type-qualified operators: current instantiation anchors discovery |
| 2.31 Dependent defaults | `generics.md` §3 | Literal source and operators: suggestion depending on the payload |
| Part 10 clarifications | As listed in 11.5 | — |

### 11.3 Structure proposal

**Keep the current layout.** No new directories or indexes.

- **New current owner:** `language/generics.md`. One owner rather than several,
  because generics is one cohesive concept: declaration, supply, identity,
  constraints, specialization, lookup, and consistency depend on each other,
  and a reader would otherwise reconstruct it from several pages. Long but
  layered, like function invocation.
- **Routers:** `index.md` gains the new owner in "Start here" and "Current
  conceptual design", drops the two legacy generic entries, and updates the
  legacy compiler-directives description. Root `README.md` is unchanged; it
  routes to the vision.
- **Retiring legacy pages:** `meta-functions.md` and `meta-types.md` are
  deleted. Their useful content is promoted, rejected (constructor inference,
  omitted types, non-`final` generic slots), or superseded.
- **Reduced legacy page:** root `compiler-directives.md` loses its `compile`,
  `requires`, and `concept` sections.
- **Raw:** `maintainer-notes/generics.md` retires (deleted, removed from the
  raw index). `maintainer-notes/compiler-directives.md` keeps only its
  generators note. `type-parameters-and-generics.md` shrinks to what remains
  deferred. Six other raw files gain captures (11.6).
- **Focused reading:** a reader reaches the generics owner from `index.md`,
  and from local handoffs in declarations, invocation, casting, compile-time
  code, and the numeric owners. Raw generic input stays outside ordinary
  reading, reached only by future work.
- **Archive effects:** none at promotion. Archival of `036` happens at closure.

### 11.4 The generics owner: outline and teaching plan

Metadata:

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers writing or reading code that works over many types or compile-time values |
| Applies To | Generic functions, operators, and types; compile-time parameters and inputs; supplying and working out arguments; instantiation and identity; constraints and `requires`; specialization; lookup inside generic bodies; extension across modules; consistency; limits, costs, and diagnostics |
| Implementation State | Not established by this repository |
| Owns | Everything in the Applies To list |
| Does Not Own | Compile-time execution, policies, and layout queries in decisions (compile-time code); callable preference structure (function invocation); alias and identity declarations (declarations and bindings, identity types); partials; reflection; numeric families |
| Source / Provenance | Legacy meta-function, meta-type, and compiler-directive input, reconciled with current compile-time, invocation, declaration, and numeric design |

**Contrasts with C++ and Rust.** Repository guidance says a concept owner
compares with another language only when that resolves a concrete likely
misunderstanding, and that full migration guides are a separate route. The
maintainer asked that readers from C++ and Rust backgrounds be served. Both
are satisfied this way:

- **One orientation section (§1b)**, right after the mental model: a short table
  of the differences that change what a C++ or Rust programmer would write,
  each linking to the section that teaches it.
- **Short inline notes** at the sections where a likely wrong assumption
  arises. Each note names the assumption, says what Zax does, and shows Zax
  source.
- **Broader comparison** (history, idioms, migration recipes) is captured in
  [raw similarity](../raw/similarity.md) for the future guide (11.6).

Each section below lists its teaching plan as: **why the reader is here**,
**what they already know**, **what changes for them**, and **what they might
wrongly try**.

**§1 Write it once, use it with many types.** Mental model, `identity$` and
`MyPair$` examples, the word *instantiation*, and the cost in one sentence
(processed once per distinct input).
- Why: deciding whether generics fit their problem.
- Knows: functions, types, `type` slots in `as`.
- Changes: one declaration yields many concrete functions or types.
- Might try: expecting a runtime "generic value" or dynamic dispatch. State
  that every instantiation is concrete and checked.

**§1b Coming from C++ or Rust.** A table of about eight rows, each linking
forward:

| You might expect | In Zax |
| --- | --- |
| `template<typename T>` or `fn f<T>` | `f$ final : (T : type)(...)(...)`; callers write `f$(Integer)(...)` or let it be worked out |
| `<T>` at a use | `$(...)`; generic types always need it, even `MyType$()` |
| C++ SFINAE, `enable_if` | No. A failing body is an error; only constraints remove candidates |
| C++20 concepts or Rust traits | Constraints are `Boolean` functions and `requires` expressions, not interfaces; bodies are checked per instantiation, like C++ templates and unlike Rust |
| C++ argument-dependent lookup | Calls involving a parameter also search the argument types' modules and the use's visible modules; disagreement between uses is an error |
| Rust's orphan rule | Any module may extend; the whole program must agree, and conflicts are diagnosed |
| C++ specialization `template<> struct X<bool>` | `requires UseType == Boolean`; the most refined constraint set wins |
| Non-type template parameters, const generics | `$` value parameters, compared by stored parts, never by a programmer's `==` |

- Why: a C++ or Rust programmer scanning for what transfers.
- Knows: their language's generics.
- Changes: which habits carry over and which do not.
- Might try: reading the table as complete semantics. Each row links to its
  section.

**§2 Two forms of generic.** A type passed as an argument (`makeDefault`,
`as`); the `$` list with types and values (`add$`, `MyBuffer$`); `[<constant>]`
ordinary inputs (`formatTime`).
- Why: choosing which form to declare.
- Knows: type parameter slots in `as`.
- Changes: a `$` list can be worked out; a type argument cannot; a
  `[<constant>]` input stays an ordinary argument.
- Might try: passing a runtime value to `[<constant>]` (show the inline error);
  expecting `makeDefault` to work out its type (it never does).

**§3 Supplying compile-time arguments.** Stating with `$(...)`, positional,
`label:`, and `:` skipping; working out from arguments, literals adapting;
from the expected result at a typed declaration; defaults, including
dependent defaults; qualifiers, references, and pointers carrying through;
generic functions must be `final`.
- Why: calling a generic and predicting what it becomes.
- Knows: ordinary argument rules and number literals.
- Changes: the four-step order; literals never coordinate; `&` and `immutable`
  carry.
- Might try: `add(3, 4.5)` (inline not-viable comment); `Integer$(Bits = 57)`
  (show `:` form only); expecting a by-value-looking parameter to copy a
  reference argument (`keep$` example); a non-`final` generic member (one
  sentence of consequence: the type's size would depend on every use).
- C++/Rust note: C++ also deduces references and qualifiers differently;
  in Zax `UseType` is exactly the argument's type.

**§4 Generic types and other generic declarations.** `MyPair$`, `$()`, unions,
variants, identity types; naming the current instantiation in full, local
aliases; one non-generic type sharing the name (`Integer` and `Integer$`).
- Why: declaring a reusable data structure.
- Knows: type, union, variant, and identity declarations.
- Changes: `$` attaches to the name; `$()` always required.
- Might try: `myValue : MyType = 5` (constructor inference does not exist;
  shown once with an inline error, since C++17 programmers expect it); bare
  `MyType` inside its own body.

**§5 Generic operators and generic function values.** `'*'$`; operator `$`
parameters are always worked out; satisfying a concrete prototype,
`identity$(Integer)` as a value; `alias variable` for a family; fully applied
`alias type`.
- Why: overloading operators generically, or storing a generic function.
- Knows: operator declarations, function values.
- Changes: no runtime value for a family.
- Might try: `myVector '*'$(Float) 2.5` (not a form; use a function);
  `myMaker := makeDefault`.

**§6 When two instantiations are the same.** Same generic and completed
arguments; defaults filled; type `==` (aliases equal, identities and
qualifiers distinct); value arguments by stored parts; excluded kinds
(pointers, references, capturing lambdas, unions); module instances; one
identity across host and target builds.
- Why: assigning or passing between instantiations, or across modules.
- Knows: identity types, aliases, generative imports.
- Changes: `Scale$(0.1 + 0.2)` and `Scale$(0.3)` differ; programmer `==` never
  decides identity.
- Might try: expecting a custom `==` to merge types.
- C++ note: close to C++20's rules for class-type template arguments.

**§7 Types are compile-time values.** Type-returning functions have no runtime
form; alias targets; computing types from values during compile-time execution
(`selectInteger`).
- Why: choosing a type by computation.
- Knows: compile-time code and policies.
- Changes: types can be returned and built from computed values, but only in
  compile-only code.
- Might try: `Integer$(LogicalBits: bits)` in a runtime-capable function
  (inline error).

**§8 Constraints.** Short form (one `type` input); `requires` with any
compile-time `Boolean`; placement on types and functions; combining with
`&&`; kinds stay visible; a false constraint is non-viability; diagnostics;
layout queries in constraints need `target size of` or `host size of`
(local example, link to compile-time code).
- Why: restricting what a generic accepts (the `Celsius as` case).
- Knows: `Boolean` functions, `wellformed`.
- Changes: constraints are ordinary code, run once per argument set.
- Might try: using a runtime input in `requires` (inline error); unqualified
  `size of` (inline error).
- C++/Rust note: no SFINAE; constraints are not traits and do not describe what
  the body may use.

**§9 Checking generic bodies.** Per-instantiation checking; hard errors with
the instantiation chain; listing needed operations in the constraint; the
lambda technique; library self-tests with compile-time assertions.
- Why: understanding where an error appears and how to make it earlier.
- Knows: `wellformed`.
- Changes: unused instantiations are not checked.
- Might try: relying on a body failure to fall back to another candidate.
- Rust note: unlike Rust, a constraint does not limit what the body may do.

**§10 Specializations.** Several declarations; term-set ranking with a
refinement example and an ambiguous example; exclusivity with `!`; exact-type
specialization through `requires UseType == Boolean`; non-generic versus
generic only between equal matches; specializations may differ in members.
- Why: providing a different implementation for some arguments.
- Knows: overloading and partial-order preference.
- Changes: ranking compares how constraints are written, never what they
  compute.
- Might try: expecting `isString` to beat `isText` because it seems narrower
  (inline ambiguity); relying on a member that a specialization lacks
  (`vector<bool>` example).

**§11 How names inside a generic body are found.** Names not involving a
parameter bind at the definition; calls involving one also search argument
types' modules and the use's visible modules; type-owned operations;
`normalize` example.
- Why: customizing library generics for a new type.
- Knows: source-ordered lookup and modules.
- Changes: free functions for a new type work; disagreement is an error.
- Might try: expecting a helper that is not visible at a use to be found.
- C++ note: wider than argument-dependent lookup in where it searches; unlike
  C++, disagreement is diagnosed.

**§12 Extending generics, and one set of choices.** Open extension; program-wide
agreement with the diagnostic; the identity-type escape; formation, later
declarations as errors, module finalization; `requires` evaluated once with
kept answers; diagnostics never depend on compile order.
- Why: adding a specialization or helper, or understanding a "chosen
  differently" error.
- Knows: §10 and §11.
- Changes: two libraries can conflict only when combined.
- Might try: declaring a helper after a use in the same module (inline error).
- Rust note: Rust forbids extension by a module owning neither side; Zax
  allows it and diagnoses conflicts.

**§13 Processing and compile points.** Processing once per distinct
compile-time input; compile points run per processing in the defining module
instance's host world; order effects; deduplication never erases identity.
- Why: predicting compile-time cost and effects.
- Knows: compile points.
- Changes: input count, not use, decides processing.
- Might try: assuming a compile point in a generic body runs once.

**§14 Limits.** `[<instantiation-depth=...>]`, default and guaranteed `4096`;
the growing-chain diagnostic.

**§15 Costs, diagnostics, source stability, and boundaries.** Costs: per-input
processing, per-qualification instantiations, code size. Diagnostics list.
Source stability: adding a specialization or helper can be an error; adding a
non-generic overload changes only equal matches; changing constraint terms
changes ranking. Boundaries: generic enums, partials, aliases, qualifier
families, protocol constraint libraries, whole-type contracts, numeric
factory APIs.

Teaching checks for the whole owner:

- **Cold reader.** §1 and §2 teach common use without §6 to §13.
- **Vocabulary before use.** *Instantiation*, *specialization*, and
  *worked out* are each defined at first use, with source first.
- **Positive-first.** Invalid forms appear only for the likely mistakes listed
  above, each with an inline error comment. No retired legacy form is taught
  as history.
- **Unaccepted facilities.** `Compilation.error` is raw input and must not
  appear unmarked. Examples that need a failure use an existing compile-time
  panic or assertion, or mark the call illustrative.

### 11.5 Exact proposed change set

**New file**

1. `language/generics.md`: the owner in 11.4.

**Current owners changed**

2. `language/declarations-and-bindings.md`
   - "Type parameter slots and type arguments": keep the local rule; add that
     a type passed as an argument is one form of generic, and link.
   - "Type-qualified operators": the current instantiation (`MyVector$(Element)`)
     anchors discovery inside a generic body; aliases and qualified type
     identities remain future.
   - "Literal operators": replace "exact generic processing syntax remains
     future work" with the payload-as-compile-time-input rule and a link.
   - "Identifier namespace and paths": add a non-generic type and a generic
     family sharing a name as a structured case.
   - "Exact aliases and property overlays": a generic function family is a
     polymorphic family for `alias variable`.
   - "Non-value definitions": state that alias definitions are allowed in
     function bodies, block-scoped (4.7).
   - "Compatible repetition": unchanged.
3. `language/function-invocation.md`
   - "Type parameter slots": link to generics.
   - "Literal result specialization": restate in generics terms; remove
     "exact generic specialization syntax ... remain future work".
   - "Narrow expected-result selection": add that it may work out a generic
     parameter, with one example.
   - "Viability", "Partial-order preference", "Demand-time ambiguity": failed
     constraint is non-viability; non-generic only between equal matches;
     term-set ranking by link.
   - "Minted implementation model": generic processing is now defined; link.
   - "Boundaries and maturity": remove generics from the future list.
4. `language/compile-time-code.md`
   - Metadata `Does Not Own`: link generics.
   - "When compile points run": per-input processing paragraph, link.
   - "Compile-time values": add `$` value parameters and `[<constant>]`
     inputs.
   - "Letting a function decide": a type-returning function has no runtime
     form.
   - New section "Layout queries in compile-time decisions" (2.22).
   - "How deep the check goes": replace the deferral with a link.
   - "Boundaries and maturity": remove compile-time parameters and generics.
5. `language/operator-catalog.md`: `target size of` and `host size of` rows
   beside `size of`.
6. `language/compiler-directives.md`: catalog entries for `[<constant>]` and
   `[<instantiation-depth=...>]`.
7. `language/operators.md`: declaration forms mention generic operators
   (`'*'$`) and link.
8. `language/operator-phrases.md`: boundary sentence; generic type-qualified
   discovery is defined, alias discovery remains future.
9. `language/casting.md`, "Writing your own `as`": replace the restriction
   paragraph with a constrained `as`.
10. `language/qualifiers.md`: replace "complete generic result resolution
    remains later function design" with a link.
11. `language/identity-types.md`: generic identity types by link;
    boundaries keep provider partials and reflection.
12. `language/type-definitions.md` and `language/variants.md`: replace "exact
    generic syntax remains future" with links; completion requirement
    unchanged. `language/unions.md` currently says nothing about generics; add
    one sentence that a union may take a `$` list, with a link.
13. `language/partials.md`: boundary question about generic code answered by
    link; generic partials remain future.
14. `language/namespaces-and-modules.md`: one sentence that generics belong
    to their module instance.
15. `language/integers.md`: `Integer$` named arguments use `:`; the family and
    `Integer` share a name by link; factory and probing are expressible
    (factory API remains future); associated types described as type-returning
    operations; argument normalization stated; boundaries updated.
16. `language/fixed-point-scalars.md`: `F = 0` normalization; full-product
    associated type by link; boundaries.
17. `language/floating-point-scalars.md`: `FastF$(N)`, `LeastF$(N)` as
    generic identity families; host and target builds; `LanguageLimits`
    unchanged; boundaries.
18. `language/endianness.md`: selector normalization before identity;
    boundaries.
19. `language/strings-and-characters.md`: `<...>` notation becomes `$(...)`;
    terminator policy as a type argument; boundaries.
20. `language/literal-source-and-operators.md`, "Every invocation has one
    concrete result": `uncommitted` as the literal-only opt-in; payload as a
    compile-time input; dependent suggestion; boundaries.
21. `language/integer-literals.md`: link from the generic-literal passage.
22. `language/iteration.md`, `language/enums.md`, `language/arrays-and-slices.md`,
    `language/composition.md`: replace "generic constraint syntax is future"
    with "constraints are written with `requires`; a standard set of protocol
    constraints remains future"; composition keeps its preferred-projection
    boundary.
23. `language/terms.md`: entries for generic, instantiation, specialization,
    constraint, `requires` clause, compile-time input, and worked-out
    parameter; link the existing uncommitted entry.

**Routers and legacy**

24. `index.md`: add generics to "Start here" and "Current conceptual design";
    remove the meta-function and meta-type legacy entries; update the legacy
    compiler-directives description.
25. Delete `meta-functions.md` and `meta-types.md`.
26. Root `compiler-directives.md`: remove `compile`, `requires`, `concept`
    sections; update the routing note.

**Raw**

27. Delete `project/raw/maintainer-notes/generics.md`.
28. `project/raw/maintainer-notes/compiler-directives.md`: keep only the
    generators note.
29. `project/raw/type-parameters-and-generics.md`: shrink to deferred items
    (11.7).
30. `project/raw/reflection.md`: capture 4.3.
31. `project/raw/analysis-controls.md`: capture 4.2 and the extension lint
    candidate.
32. `project/raw/cpu-provider-model.md`: capture 4.1 residual design.
33. `project/raw/build-and-dependencies.md`: capture 4.5.
34. `project/raw/feature-catalog.md`: capture 4.4; reroute the polymorphic-types,
    meta-function-parameters, and partial-types rows to the generics owner.
35. `project/raw/callable-selection.md`: record that generic specialization
    preference is now owned by generics (4.6).
36. `project/raw/similarity.md`: capture C++ and Rust generic comparison input.
37. `project/raw/cross-cutting-audit.md`: the `meta-functions.md` continuation
    entry is resolved by retiring the page.
38. `project/raw/README.md`: remove the generics notes row; update the
    compiler-directive notes and type-parameters rows.

**Unchanged:** `project/README.md` (until closure), `README.md`, the two
operating-prompt sources, `project/documentation.md`, archived work.

### 11.6 Teaching plans for other owners' changed sections

| Section | Why the reader is there | What they know | What changes | What they might wrongly try |
| --- | --- | --- | --- | --- |
| Casting, "Writing your own `as`" | Declaring a conversion | `as` with a type slot | A constrained destination | Writing one `as` for every type and relying on body failure |
| Compile-time code, layout queries | Writing a constraint or `if compile` on sizes | `size of`, environments | Explicit environment in decisions | Plain `size of Integer` in an `if compile` |
| Compile-time code, compile-time values | Asking what counts as compile-time | The current list | `$` values and `[<constant>]` inputs | Treating a varying local as compile-time |
| Function invocation, preference | Predicting which overload wins | Partial order | Generic specialization slots | Expecting generic to lose to a converting non-generic |
| Declarations, identifier namespace | Naming a type | One namespace | `Integer` and `Integer$` coexist | Declaring two non-generic types of one name |
| Declarations, non-value definitions | Shortening names inside a function | Aliases in types and namespaces | Function-body aliases | Expecting the alias to escape the block |
| Integers, family syntax | Naming an unusual width | `MyI57` example | `label:` arguments | `LogicalBits = 57` |
| Literal source, concrete result | Writing a custom literal | `uncommitted` | Payload-dependent suggestion | Expecting value magnitude to rank overloads |
| Strings, encodings and policies | Choosing a code-page string | `MbcsString<...>` notation | `$(...)` form | Treating the policy as a runtime value |

Each owner keeps its own mental model first and adds the handoff where its
reader meets the generic case, not at the first related heading.

### 11.7 Deferred and raw destinations

What remains in `project/raw/type-parameters-and-generics.md` after promotion:

- qualifier-parameterized declaration families;
- generic aliases (family and partially applied) and generic forwards;
- alias type-qualified discovery and qualified type-identity lookup anchors;
- integer factory API, relational signedness pairs, a general
  associated-type facility, intrinsic parameter validation for fixed and
  floating families, and `LanguageLimits` values;
- cursor-protocol, array, and allocation-policy constraint libraries;
- Nothing policy per specialization;
- string terminator-policy contracts;
- whole-type abstract contracts;
- generic partials and generic enums;
- an exact-type specialization form (2.16);
- optional-depth substitution, retained as a constraint and also taught in the
  generics owner.

Other destinations are listed in Part 4 and 11.5. Every capture there names
its activation pressure.

### 11.8 Applying the change set

The change set touches about 35 files. It is one coherent change, applied in
two passes:

1. **Owner pass:** write `generics.md` from 11.4, building the reader's model
   first and then tracing every Part 2 finding into its section.
2. **Integration pass:** apply items 2 to 38, then validate links, anchors, and
   rendering, check that every Part 2 and Part 10 item is traced, and confirm
   that no current owner cites this work item.

After promotion, an agent that has not read this record reviews the new owner
and the changed sections as a direct-entry reader, as the validation rules
require, and reports what it cannot follow, leaked discovery history, and
examples without an evident purpose.

### 11.9 Points to confirm before promotion

1. **One owner** (`language/generics.md`) rather than several.
2. **C++ and Rust contrasts** as one orientation table plus inline notes, with
   broader comparison captured in raw similarity.
3. **The extension lint** goes to raw analysis controls as an undecided
   candidate.
4. **Two-pass application** of one change set, with an independent
   cold-reader review afterward.

The maintainer confirmed all four, accepted `Compilation.error` in promoted
examples (with the raw capture noting that the spelling is under review), and
authorized promotion.

## Part 12: promotion record

Applied 2026-11-03. Every change-set item in 11.5 was applied. Retired files
were deleted only in the working tree; the maintainer's staged boundary still
holds their earlier versions. No version-control state was changed.

### 12.1 What was applied

- **Owner pass:** `language/generics.md` written from 11.4.
- **Integration pass:** items 2 to 23 applied by one delegated agent and
  reviewed; items 24 to 38 (routers, retirements, raw captures) applied
  directly.
- **Validation:** every relative link and heading anchor in the live tree
  resolves; no current owner cites numbered work or links to `project/raw/`.

Interpretations made during integration, kept:

- An environment-named type such as `Target.Integer` already says which
  environment a layout query means, so existing `if compile size of
  Target.Integer > 4` examples remain valid under the decision-position rule.
- The `isTemperature` constraint accepts only `Fahrenheit`, because the
  conversion body produces Fahrenheit. The constraint lists every destination
  the body can produce.
- The `once`-related "generic-specialization behavior" deferrals in function
  invocation, enums, and declarations were left in place, because generics does
  not cover `once` state.
- `host size of` means the compiler host, the environment `CompilerHost.`
  names.
- A literal payload, although declared as a reference, is compared by its
  string contents when identifying a processing.

Left unchanged as still accurate: `principles.md` ("future generic and
metaprogramming relationships"; generic matching preserving partial-order
discipline), `structural-shapes-and-compatibility.md` (whole-type conformance
remains future contract work), and `mixfix-operators.md` (exact generic
substitution in mixfix remains future).

### 12.2 Cold-reader review

An agent that had not read this record reviewed `generics.md` and the changed
sections as a direct-entry reader, from general, C++, and Rust backgrounds. Its
findings and dispositions:

| Finding | Disposition |
| --- | --- |
| `firstOf(myFlags)` used the wrong variable | Fixed: `firstOf(flags)` |
| `MyStore$` defined two incompatible ways | Fixed: the second is `MyPackedStore$` |
| `makeBuffer$` example shipped a host-sized table | Fixed: the host use is compile-time scratch space, with a sentence that shipped data is a target decision |
| `host size of` versus `CompilerHost.` | Fixed: stated that they name the same environment |
| `isTemperature` differed between owners; `Kelvin` undefined | Fixed: `Fahrenheit` only in both, with the full body |
| Integers said user generics compare "as written" | Fixed: "by the ordinary identity rules", linked |
| Literal payload is a reference, while references are not value arguments | Fixed: payloads compare by string contents |
| `isNumeric` used before definition; undefined helpers; `T`/`U` names | Fixed: `isNumeric` defined at first use; a note that helpers and placeholder types are illustrative; `Fmt$` uses `Label`/`Amount` |
| `makeDefault` returned nothing | Fixed: returns `UseType` |
| "Replacement constructor" unexplained | Fixed: rephrased |
| `outward` unlinked; `alias variable` example did not show `$` | Fixed: linked; example uses `identity` with and without `$` |
| History-like wording ("already", "no longer generic", "future generic aliases") | Fixed |
| Contrast table lacked reference/qualifier carry and silent-ODR rows | Fixed: two rows added |
| `identity(42)` calling `identity$` explained only later | Fixed: stated in the opening |
| When an agreement failure is reported | Fixed: at the latest when the program is assembled |
| Wording such as "guarantees" and "on every compiler" | Kept: these state aligned design requirements in a conceptual-design owner |
| Whether `[<constant>]` functions are generics and must be `final` | **Open question for the maintainer**; see 12.3 |
| Whether an unresolvable name not involving a parameter is reported in an unused generic | **Open question for the maintainer**; see 12.3 |

### 12.3 Open questions raised by validation

These were not decided during promotion. `generics.md` states each under
"Boundaries and maturity" rather than choosing an answer.

1. **Non-`final` functions with `[<constant>]` inputs.** 2.6 records that
   `[<constant>]` is part of the prototype, so that a replacement for a
   non-`final` function must honor it. 2.2 and 2.6 also record that a function
   with `[<constant>]` inputs has no runtime function value. A replaceable slot
   holds a function value, so a non-`final` function with `[<constant>]`
   inputs has nothing to install. The promoted owner states `[<constant>]` as
   part of the prototype and lists the question; the replacement sentence was
   not promoted.

   ```zax
   formatTime varying : (result : String)(
     format [<constant>] : String readonly,
     value : TimePoint
   ) = { ... }

   formatTime = otherFormatter   // what function value could this install?
   ```

2. **Errors in code that does not involve a parameter, in an unused generic.**
   2.20 binds such names where the generic is defined. Whether an unresolvable
   one is reported even when the generic is never instantiated was not
   decided.

   ```zax
   process$ final : (UseType : type)()(value : UseType) = {
     helperThatDoesNotExist(5)   // reported at the definition, or only when instantiated?
   }
   ```

Both need discussion and a separately authorized edit.

### 12.4 Post-promotion review by the maintainer

The maintainer reviewed the promoted text. Authorized follow-up edits:

- `compile-time-code.md`, "Layout queries in compile-time decisions":
  - removed the confusing "buffer … shipped into the program" sentence;
  - restated when compile-time code decides for the target program (a
    constraint choosing a specialization; an `if compile` choosing code);
  - gave the decision-position list an introducing sentence;
  - replaced "anything those call" with "any function called while evaluating
    one of these, such as `isSmall` when it is used as a constraint"; and
  - reworded the `Target.Integer` sentence.
- `generics.md`:
  - "say so" became "negate one's constraint in the other"; and
  - the identity-type wrapper sentence was corrected. Under open extension,
    another module that imports the wrapper can still extend it, so the
    wrapper does not make conflicts impossible. It separates the extender's
    extensions from those others wrote for the original type.
- `terms.md`: "a distinct set of them" became "each distinct combination of
  compile-time arguments it receives".
- Outside the generics change, at the maintainer's request, "say so" phrasing
  was reworded in `core-flow-control.md`, `integer-literals.md`, `casting.md`,
  and `optional-values.md`.

Answers given during that review, recorded as aligned:

- **A computed type is never a constraint.** In
  `result uncommitted : suggestHexInteger(payload)`, the call returns a `type`,
  so it supplies a type. A short-form constraint is a bare function name in a
  parameter's kind position, with exactly one `type` input and a `Boolean`
  result. A function returning the type `Boolean` yields the type `Boolean`,
  never a truth value. A bare name of a zero-input function in a type position
  is read as a constraint and rejected; with `()` it is a computed type.
- **Constraints using compile-time values go in `requires`.** The short form
  receives only the type parameter it constrains. A `requires` clause may use
  any compile-time input, including a literal payload or a `[<constant>]`
  input:

  ```zax
  operator literal 'h' final : (
    result uncommitted : suggestHexInteger(payload)
  )(
    payload : String immutable readonly final &
  ) unbound requires isValidHex(payload) = {
  }

  bad := h'GG'   // error: no viable `h` literal; isValidHex("GG") is false
  ```

- **Aliases when a non-generic type and a family share a name.** The `$`
  decides the target:

  ```zax
  MyPlain :: alias type Foobar               // the non-generic type
  MyInt :: alias type Foobar$(Integer)       // one instantiation
  MyFamily$ :: alias type family Foobar$     // illustrative future form: the family
  ```

- **Layout queries in decisions are always explicit.** The promoted text had
  treated a query on `Target.Integer` as already explicit. The maintainer
  rejected that: `Target.` and `CompilerHost.` are only paths, and nothing in a
  type marks its layout as fixed. A layout agreeing for one host and target
  pairing, or because a platform profile says so, is never assumed. The
  aligned rule: in a compile-time decision, every layout query names its
  environment with `target` or `host`, whatever the type, so
  `size of U32` and `size of Target.Integer` are errors there. Common queries
  have dedicated forms (`target size of`, `host size of`, `target alignment
  of`, `host alignment of`). Rarer facts come from a reflection layout
  descriptor obtained from a named environment, such as an illustrative
  `Compilation.target.layout(UseType)`. An explicit `Compilation.active`
  request is acceptable, because the choice is named. Applied to
  `compile-time-code.md` (rule, examples, and the two `if compile` examples),
  `type-definitions.md` (its `if compile` example), and `operator-catalog.md`
  (alignment rows); captured in raw CPU provider model and reflection. The
  earlier "is environment fixed" reflection idea is dropped.
- **Superseded: no plain layout queries at all.** Discussion then showed a
  decision-position list could not be complete: a type-returning function such
  as `selectBuffer` decides for the target, and a compile-time function could
  return a build-machine `size of` that is used far away in a decision. The
  danger is created where the fact is computed. The aligned final rule: every
  layout query names its environment, `active`, `target`, or `host`, in all
  code. There is no plain `size of`, `alignment of`, or `offset of`. `active`
  means the world running the code (target in the program, compiler host while
  compiling) and is legal everywhere, including decisions, because it is
  explicit; an optional lint may flag `active` in a decision. Applied to
  `compile-time-code.md` (section renamed "Layout queries name their
  environment", with the reasoning and examples; "Which definitions apply
  while compiling" examples; compile-time values; diagnostics),
  `operator-catalog.md` (all three environments for size, alignment, and
  offset), `generics.md`, and prose in `arrays-and-slices.md`,
  `compiler-directives.md`, `execution-context.md`, `partials.md`,
  `pointers-and-arenas.md`, `structural-shapes-and-compatibility.md`,
  `terms.md`, and `type-definitions.md`. The reasoning, and the remaining gap
  (an explicit host value carried into a target decision; environment-carrying
  size types as the complete fix), are captured in raw CPU provider model;
  descriptor wording in raw reflection; the lint candidate in raw analysis
  controls. Legacy root pages that still use plain `size of` are left for their
  own reviews.
- **`host` is kept, with a note for GNU readers.** The maintainer asked whether
  `host` should be `build`. Rust, Zig, Go, LLVM, and CMake use *host* for the
  machine running the compiler, as Zax does; GNU autoconf, Meson, and Nix use
  *build* for it and *host* for where the program runs. `host` is kept because
  it matches the toolchains closest to Zax and is used consistently across
  the language, and because `Compilation.build` already names build
  configuration. A one-line clarification was added where the term is defined
  (`compile-time-code.md`, "Which definitions apply while compiling") and in
  the `terms.md` entry; the fuller comparison is captured in raw similarity.
- **Enum value arguments create distinct instantiations per enumerator.**

  ```zax
  Channel :: enum { Red, Green, Blue }

  MyPlane$(channel : Channel) :: type { samples : U8[256] }

  a : MyPlane$(Channel.Red)
  b : MyPlane$(Channel.Red)     // same type as a
  c : MyPlane$(Channel.Green)   // a different type
  ```

## Part 13: closure

Closed 2026-11-04 by the maintainer. This record is historical, non-normative,
and audit-only.

Dispositions at closure:

- **Aligned findings (Part 2, Part 10):** promoted to `language/generics.md` and
  the integrated owners listed in 11.5 and Part 12.
- **Captures (Part 4):** written into raw analysis controls, reflection, CPU
  provider model, build and dependencies, feature catalog, callable selection,
  and similarity.
- **Deferred items (Part 7, 11.7):** live in raw type parameters and generics,
  with activation pressure.
- **Open questions (12.3):** both stated under "Boundaries and maturity" in
  `language/generics.md`: non-`final` functions with `[<constant>]` inputs, and
  whether an unresolvable name not involving a parameter is reported in an
  unused generic.
- **Extension lint and `active`-in-decision lint:** undecided candidates in raw
  analysis controls.
- **Teaching-debt observations:** none recorded.

Successor: work item `037` will address variable-length (variadic) arguments.
Its initiating input and reading scope are to be aligned with the maintainer
before the file is created.
