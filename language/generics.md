# Zax generics

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers writing or reading code that works over many types or compile-time values |
| Applies To | Generic functions, operators, and types; compile-time parameters and inputs; supplying and working out compile-time arguments; instantiation and identity; constraints and `requires`; specialization; lookup inside generic bodies; extension across modules; consistency of choices; limits, costs, and diagnostics; not a formal grammar or a compiler architecture |
| Implementation State | Not established by this repository |
| Owns | The two forms of generic; `$` lists and `$(...)` supply; `[<constant>]` inputs; per-input processing; filling compile-time parameters; types as compile-time values; instantiation identity; short-form constraints and `requires` clauses; specialization ranking; lookup inside generic bodies; open extension and program-wide agreement; instantiation formation and consistency; `[<instantiation-depth=...>]` |
| Does Not Own | Compile-time execution, execution policies, and layout queries ([compile-time code](compile-time-code.md)); the structure of callable preference ([function invocation](function-invocation.md#candidate-selection)); alias and identity declarations ([declarations and bindings](declarations-and-bindings.md), [identity types](identity-types.md)); partials; reflection; the numeric families ([integers](integers.md)) |
| Source / Provenance | Legacy meta-function, meta-type, and compiler-directive input, reconciled with current compile-time, invocation, declaration, literal, and numeric design |

## Write it once, use it with many types

A **generic** is code written once that the compiler processes again for each
set of compile-time inputs it is used with:

```zax
identity$ final : (UseType : type)(result : UseType)(value : UseType) = {
  return value
}

first := identity(42)          // UseType is Integer
second := identity("hello")    // UseType is String
third := identity$(U8)(7)      // UseType stated as U8
```

`identity$` has three parenthesized lists. The first, marked by the `$` on the
name, holds **compile-time parameters**: here one type, `UseType`. The other two
are the ordinary result and input lists. The `$` marks the declaration; the
function's name is still `identity`, which is why callers can write
`identity(42)`.

Each distinct set of compile-time arguments produces one **instantiation**: an
ordinary concrete function, checked and compiled like any other. `identity(42)`
calls the instantiation for `Integer`; `identity("hello")` calls a separate one
for `String`. Nothing is decided at runtime, and there is no generic value that
exists while the program runs.

Types can be generic too:

```zax
MyPair$(First : type, Second : type) :: type {
  first : First
  second : Second
}

point : MyPair$(Integer, Integer)
entry : MyPair$(String, U32)
```

`MyPair$(Integer, Integer)` and `MyPair$(String, U32)` are two different
concrete types, each with its own layout.

The cost is visible: every distinct instantiation is compiled separately, and
each adds code to the program unless the compiler can merge identical output.

Examples on this page call small helper functions, such as `isText` or
`canCompare`, and use placeholder types, such as `MyFlag`, without declaring
them. Read them as ordinary declarations whose meaning their names suggest.

## Coming from C++ or Rust

The rest of this page teaches Zax directly. If you know C++ templates or Rust
generics, these are the differences most likely to change what you write:

| You might expect | In Zax | See |
| --- | --- | --- |
| `template<typename T>` or `fn f<T>` | `f$ final : (T : type)(...)(...)`. Callers write `f$(Integer)(...)`, or let the compiler work the type out | [Two forms](#two-forms-of-generic) |
| `MyType<int>` at a use | `MyType$(Integer)`. A generic type always needs its list, even `MyType$()` | [Generic types](#generic-types-and-other-generic-declarations) |
| SFINAE or `enable_if` | None. An error inside a generic body is an error; only constraints remove candidates | [Constraints](#constraints) |
| C++20 concepts or Rust traits | Constraints are `Boolean` functions and `requires` expressions. They decide what is accepted, not what the body may use; bodies are checked per instantiation, as in C++ | [Checking bodies](#checking-generic-bodies) |
| C++ argument-dependent lookup | Calls involving a parameter also search the argument types' modules and the modules visible where the generic is used. Uses that disagree are an error | [Lookup](#how-names-inside-a-generic-body-are-found) |
| Rust's orphan rule | Any module may extend a generic. The whole program must agree, and conflicts are diagnosed | [Extending generics](#extending-generics-from-other-modules) |
| `template<> struct X<bool>` | A declaration with `requires UseType == Boolean`. The most refined constraint set wins | [Specializations](#specializations) |
| Non-type template parameters or const generics | `$` value parameters, compared by their stored parts, never by a programmer's `==` | [Identity](#when-two-instantiations-are-the-same) |
| A by-value `T` drops references and `const` (C++), or `T` is always a value type (Rust) | A worked-out type is exactly the argument's type: a reference argument makes `UseType` a reference, and qualifiers stay | [Working out](#working-out-from-arguments) |
| A one-definition-rule conflict is silent (C++) | Uses that choose differently for one instantiation are a compile error, reported at the latest when the program is assembled | [Agreement](#every-use-must-agree) |

## Two forms of generic

A declaration takes compile-time inputs in one of two ways. Both are generics:
the compiler processes the code once per distinct input. They differ in how
the caller supplies the input.

### A type passed as an argument

A parameter whose kind is `type` may sit in the ordinary input list. The caller
passes the type itself, exactly like any other argument:

```zax
makeDefault final : (result : UseType)(UseType : type) = {
  value : UseType
  return value
}

zero := makeDefault(Integer)
empty := makeDefault(String)
```

The conversion operator `as` uses this form. Its right operand is the
destination type:

```zax
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

display := boiling as Fahrenheit
```

A type passed as an argument is never worked out from other arguments. The
caller always writes it. The local rule for these slots is in
[declarations and bindings](declarations-and-bindings.md#type-parameter-slots-and-type-arguments).

### A `$` list of compile-time parameters

`$` on a declaration's name says that the first list after `:` holds
compile-time parameters. They may be types or compile-time values:

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
plain := add(100, 50)               // OperandType worked out; ResultType defaulted
```

The caller may state compile-time arguments with `$(...)`, or omit them and let
the compiler fill them, as described in
[supplying compile-time arguments](#supplying-compile-time-arguments).

A value parameter writes its kind like any parameter. Its argument must be a
[compile-time value](compile-time-code.md#compile-time-values):

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
  each elem : in myArray {
    elem = value as ResultType
  }
  return myArray
}

tens := fillArray(5)              // 10 Integer elements; ValueType worked out as Integer
fives := fillArray$(5)(10)        // 5 Integer elements
bytes := fillArray$(:, U8)(128)   // length keeps its default; 10 U8 elements
```

### Compile-time inputs in the ordinary input list

A third kind of compile-time input is not a generic parameter, but it is
processed the same way. Sometimes a function needs a compile-time value but
callers should write an ordinary call. A format string that the function checks
while compiling is the typical case. The `[<constant>]` directive, written
after the parameter's name, requires the argument to be a compile-time value:

```zax
formatTime final : (result : String)(
  format [<constant>] : String readonly,
  value : TimePoint
) = {
  if compile !looksLikeTimeFormat(format) {
    Compilation.error("formatTime: invalid time format")   // illustrative; see below
  }
  // ...
}

text := formatTime("%H:%M", now())     // fine: the format is a compile-time value
other := formatTime(userFormat, now()) // error: userFormat is a runtime value
```

`[<constant>]` is part of the prototype, like a default. `readonly` is enough
to keep the value stable inside the body; an `immutable` parameter also needs
`final`, so that its value cannot be replaced in place.

Examples on this page use `Compilation.error("message")` for a compiler error
carrying the author's message; its exact spelling is listed under
[boundaries and maturity](#boundaries-and-maturity).

### When code is processed once per input

> A function with compile-time inputs (type arguments, `$` parameters, or
> `[<constant>]` inputs) is processed once per distinct set of those inputs.
> Every compile point in its body runs once per processing.

```zax
formatTime("%H:%M", now())   // one processing, for "%H:%M"
formatTime("%Y", now())      // another, for "%Y"
formatTime("%H:%M", later)   // reuses the first
```

The rule counts inputs, not whether the body happens to use them. A compile
point can have effects on the build machine, so how many times it runs must not
depend on what the compiler can prove about other code.

Code without compile-time inputs is processed once, even when its compile
points read globals or call functions whose results vary:

```zax
myBuildCount : Integer = 0

describeBuild final : ()() = {
  compile{
    ++myBuildCount       // runs once, however many times describeBuild is called
  }
}
```

[Processing and compile points](#processing-and-compile-points) covers order,
module instances, and host and target builds.

## Supplying compile-time arguments

Each `$` parameter is filled by the first of these that applies:

1. **Stated** with `$(...)`.
2. **Worked out from the arguments.**
3. **Worked out from the expected result**, only at a fully typed declaration.
4. **Its default.**

```zax
a := add$(U8)(100, 50)        // 1. ResultType stated as U8
b : Short = add(100, 50)      // 3. ResultType from the typed declaration: Short
consume(add(100, 50))         // 4. no typed declaration: ResultType defaults to Integer
```

### Stating arguments

`$(...)` follows the ordinary argument rules: positional arguments from the
left, `label:` for named arguments, and `:` to acknowledge a skipped slot. See
[positional and named inputs](function-invocation.md#positional-and-named-inputs).

```zax
first := fillArray$(5)(10)
second := fillArray$(length: 3, ResultType: U8)(1)
third := fillArray$(:, U8)(128)

MyOdd :: alias type Integer$(LogicalBits: 57, Signedness: Sign.Signed)
```

### Working out from arguments

Arguments that already have a type are used first. Number literals then take
the resulting type, exactly as a literal takes a non-generic parameter's type.
Only when every argument for a parameter is a literal does each literal take
its own type, as `value := 0` would. Literals never coordinate with each other:

```zax
myByte : U8 = 7

c := add(myByte, 1)        // myByte gives OperandType = U8; the literal 1 becomes U8
d := add(1, 2)             // both literals: each becomes Integer
e := add(3, 4.5)           // error: no viable add; 3 becomes Integer, 4.5 a real type
f := add$(:, Float)(3, 4.5) // OperandType stated: both literals become Float
```

A shared parameter that cannot be worked out to one type makes the candidate
not viable. It is not an error inside the body.

A worked-out type is exactly the argument's type, including its qualifiers and
any reference or pointer shape:

```zax
keep$ final : (UseType : type)()(value : UseType) = {
  myStored := value
}

myFlag : Boolean = true
myView : Boolean & = myFlag

keep(myFlag)   // UseType is Boolean: value is a copy
keep(myView)   // UseType is Boolean &: value is a reference, under reference lifetime rules
```

A parameter that looks by-value can therefore refer to the caller's storage
when the argument is a reference. A generic that always wants a value removes
the reference explicitly; type reflection owns the operations that strip
qualifiers, references, and pointers.

The parameter's own declaration applies its qualifiers on top of the
worked-out type, the way an alias overlays a pre-qualified type. A qualifier
written more locally replaces an inherited one on the same axis, and omitted
axes are inherited (see
[exact aliases and property overlays](declarations-and-bindings.md#exact-aliases-and-property-overlays)):

```zax
show$ final : (UseType : type)()(value : UseType readonly) = { }

myConstant : Boolean immutable = true
show(myConstant)   // UseType is `Boolean immutable`; value is `Boolean immutable readonly`
```

As with aliases, an overlay changes what is requested, never what the source
can provide:

```zax
hold$ final : (UseType : type)()(value : UseType) = {
  item varying : UseType = value   // error for hold$(Foo final): access cannot exceed the place
}

myFixed : Foo final = makeFoo()
hold(myFixed)
```

Different qualifications of the same argument type produce different
instantiations. `show(myConstant)` and `show(myOther)` compile the body twice
when one is `immutable` and the other is not.

### Working out from the expected result

At a fully typed declaration, the declared type may fill a parameter that the
arguments did not:

```zax
parseValue$ final : (ResultType : type = Integer)(result : ResultType)(
  text : String readonly &
) = {
}

count : U16 = parseValue(input)   // ResultType is U16
other := parseValue(input)        // no declared type: ResultType defaults to Integer
use(parseValue(input))            // an enclosing call never supplies it: Integer
```

This is the same narrow boundary that ordinary
[expected-result selection](function-invocation.md#narrow-expected-result-selection)
uses. An expected result never reaches through an enclosing call or operator.

### Defaults

A default may use any other compile-time input of the same declaration:
earlier or later `$` parameters, type arguments, and `[<constant>]` inputs.
Defaults are evaluated in dependency order, and a cycle is an error:

```zax
fillBuffer$ final : (
  Element : type,
  capacity : Integer = 4096 / target size of Element
)(
  result : Element[capacity]
)() = {
}

small := fillBuffer$(U64)()   // capacity is 512
```

A default decides something about the instantiation the program uses, so a
layout query in it usually wants `target`; see
[layout queries name their environment](compile-time-code.md#layout-queries-name-their-environment).

### Generic functions are `final`

A generic function must be `final`. A replaceable generic member would need a
separate slot for every instantiation used anywhere in the program, so the
enclosing type's size could not be known until every use was known.

A programmer who needs a replaceable slot declares a concrete one for the
instantiation they choose:

```zax
myAdd : (result : Integer)(value1 : Integer, value2 : Integer) = add
```

## Generic types and other generic declarations

A `$` list attaches to the name of a type, union, variant, or identity type:

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

A generic type is always written with its argument list, even when every
parameter has a default:

```zax
MyBuffer$(capacity : Integer = 64) :: type {
  storage : Byte[capacity]
}

small : MyBuffer$(16)
standard : MyBuffer$()     // every parameter defaulted; `()` is still required
```

The compiler never works out a generic type's arguments from a constructor.
The type decides its layout and its constructors, so it must be known first:

```zax
MyBox$(Content : type) :: type {
  content : Content
  +++ final : ()(input : Content) = {
    _.content .= input
  }
}

myBox : MyBox$(Integer) = [{ 5 }]   // fine
other : MyBox = [{ 5 }]             // error: MyBox is a generic family; write MyBox$(Integer)
```

Each instantiation must become one complete concrete type before its size,
layout, generated lifecycle operations, union admissibility, or variant
coverage are decided, as for any
[type definition](type-definitions.md#partial-and-generic-completion-boundaries).

### Inside a generic type's body

Inside the body, the current instantiation is written in full. It anchors
type-qualified operations the way a non-generic type's name does:

```zax
MyVector$(Element : type) :: type {
  items : Element[4]

  operator binary '+' final : (
    result : MyVector$(Element)
  )(
    other : MyVector$(Element) readonly &
  ) readonly = {
  }
}
```

A local alias gives it a shorter name:

```zax
MyVector$(Element : type) :: type {
  ThisVector :: alias type MyVector$(Element)

  operator binary '+' final : (result : ThisVector)(other : ThisVector readonly &) readonly = {
  }
}
```

### Sharing a name with a non-generic type

One non-generic type and a generic type family may share a name. The
spellings never collide:

| Spelling | Meaning |
| --- | --- |
| `Integer` | the non-generic type |
| `Integer$(...)` | an instantiation of the family |
| `Integer$` | the family itself, reserved for naming families (see [boundaries](#boundaries-and-maturity)) |

```zax
myCount : Integer
myOdd : Integer$(LogicalBits: 57, Signedness: Sign.Signed)
```

There may be at most one non-generic type with that name, and the family may
have any number of [specializations](#specializations). The intrinsic integer
family uses this; see [integers](integers.md).

### Naming a generic

A generic function has no single runtime function value, because a runtime
call has nowhere to carry a type. A fully typed function-value declaration
picks one instantiation, either worked out from its prototype or stated:

```zax
myIdentity : (result : Integer)(value : Integer) = identity          // UseType worked out
myExplicit : (result : Integer)(value : Integer) = identity$(Integer) // UseType stated
```

`identity$(Integer)` with no argument list names that instantiation as a value.

To give the whole family another name, use `alias variable`, which denotes a
polymorphic function family. The alias is used exactly like the original,
with or without `$`:

```zax
sameValue :: alias variable identity
a := sameValue(42)          // UseType worked out as Integer
b := sameValue$(U8)(7)      // UseType stated
```

A function with a type argument or a `[<constant>]` input has no runtime
function value of its own either; a runtime call could not guarantee a
compile-time argument.

A fully applied type alias names one instantiation:

```zax
MyFlags :: alias type MyBits$(Boolean)
```

Naming a generic type family, or applying only some of its parameters, is
future generic-alias work. Bare `MyBits$`, with no parentheses, is reserved for
it.

## Generic operators

An operator declaration may have its own `$` list. `$` attaches to the quoted
operator words, as it attaches to a function name. Here `isNumeric` is a
[constraint](#constraints), a `Boolean` function that accepts numeric types:

```zax
isNumeric final : (result : Boolean)(UseType : type) = {
  return UseType == Integer || UseType == Float
}

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

scaled := myVector * 2.5   // Scalar worked out from the right operand
```

A phrase operator writes it the same way, for example
`operator post unary 'cluck loudly'$`.

An operator use has no place to state compile-time arguments, so an operator's
`$` parameters are always worked out from its operands or defaulted. To choose
the instantiation explicitly, write an ordinary generic function and call it
with `$(...)`.

An operator that only uses its enclosing type's parameters, like the `+` above,
needs no `$` of its own.

## When two instantiations are the same

The same generic with the same completed arguments is the same instantiation,
wherever it is written and in every module:

```zax
a : MyPair$(Integer, String)
b : MyPair$(Integer, String)
c : MyPair$(String, Integer)

a = b   // same type
a = c   // error: different instantiations are different types
```

Generic types therefore work in module interfaces:

```zax
// Module Flags
makeFlags final : (result : MyBits$(MyFlag))() = { ... }

// Module App
myFlags : MyBits$(MyFlag) = makeFlags()   // the same type, with the same layout
```

### Comparing arguments

- **Defaults are filled in first.** With `MyBuffer$(capacity : Integer = 64)`,
  `MyBuffer$()` and `MyBuffer$(64)` are the same type.
- **Type arguments compare exactly.** An alias is its target. An identity type
  is a different type from the one it is based on. Qualifiers are part of the
  type:

  ```zax
  MyCount :: alias type Integer
  MyId :: identity admit expose type Integer

  sameCount := MyCount == Integer            // true
  sameId := MyId == Integer                  // false
  sameFlag := Boolean immutable == Boolean   // false
  ```

  Looser comparisons, such as ignoring qualifiers, belong to type reflection.
- **Value arguments are the same when every stored part is the same, all the
  way down:**
  - scalars by exact value: integers by value, floating-point values including
    the sign of zero and the NaN payload, characters by code point, enums by
    enumerator;
  - strings and arrays element by element;
  - composite types declared stored member by declared stored member,
    including private members. Padding and layout never matter, because they
    are not members;
  - variants by alternative, then value.

  Pointers, references, capturing lambdas, and unions are not allowed as value
  arguments. A pointer or reference cannot cross into the program, and a union
  has no defined active member to compare.

A programmer-defined `==` never decides identity, so no user code runs when the
compiler looks up an instantiation. The comparison walks the same parts that
[values crossing into the program](compile-time-code.md#values-crossing-into-the-program)
use.

```zax
Scale$(factor : Binary64) :: type { }

x : Scale$(0.1 + 0.2)
y : Scale$(0.3)
// Different types: the two values differ in their last bits.

z : Scale$(-0.0)
w : Scale$(0.0)
// Different types: the sign of zero is part of the value.
```

### Module instances

Generics behave like every other declaration under
[generative imports](namespaces-and-modules.md#generative-imports-and-injection).
Two instances of one library declare two different generics:

```zax
first : GeometryA.MyList$(Integer)
second : GeometryB.MyList$(Integer) = first
// error: the generics belong to different module instances
```

## Types are compile-time values

A type identity is a compile-time value of kind `type`. A function may take
one, and may return one:

```zax
chooseDefaultTemperature final : (result : type)() = {
  return Fahrenheit
}

MyTemperature :: alias type chooseDefaultTemperature()
```

A type has no runtime representation, so a function that returns a type cannot
run at runtime. It behaves as if its
[execution policy](compile-time-code.md#letting-a-function-decide-execution-policies)
were `compile`, and declaring it `[<execute=runtime>]` is an error. Taking a
type as an input has no such effect: `as` takes a type and runs at runtime.

A type-returning call used as an alias target finishes during compilation and
yields one concrete type.

### Building types from computed values

In code that only ever runs at compile time, a `$` argument may be any value
that exists while that code runs, including a local computed in a loop:

```zax
selectInteger final : (result : type)(minimumBits : Integer) = {
  width := 8
  while width < minimumBits {
    width *= 2
  }
  return Integer$(LogicalBits: width, Signedness: Sign.Signed)
}

MySelected :: alias type selectInteger(13)   // LogicalBits 16
```

This applies to type-returning functions, `[<execute=compile>]` functions, and
`compile{...}` blocks: code with no runtime form. Code that may run at runtime
must use compile-time values known when its body is processed, because
otherwise a runtime call could ask for a new type:

```zax
makeWidth final : (result : Integer)(bits : Integer) = {
  value : Integer$(LogicalBits: bits)   // error: bits is not a compile-time value here
  return 0
}
```

An instantiation requested during compile-time execution is an ordinary use.
Identity, [agreement](#extending-generics-from-other-modules), and the
[instantiation limit](#limits) all apply, and a failure reports the
compile-time call chain.

## Constraints

`type` accepts any type. Often a generic should accept only some. The
`Celsius` conversion above is selected even for a destination it cannot
produce:

```zax
label := boiling as String   // selected, then fails inside the body: String has no `degrees`
```

A **constraint** states what a generic accepts. Constraints run during
compilation, before any call happens.

### The short form

Name a `Boolean` function with one `type` input in place of `type`. This
revises the earlier `Celsius` conversion:

```zax
isTemperature final : (result : Boolean)(UseType : type) = {
  return UseType == Fahrenheit
}

Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : isTemperature
  ) readonly = {
    result .= [{
      .degrees = degrees * 9.0 / 5.0 + 32.0
    }]
  }
}

display := boiling as Fahrenheit   // accepted
label := boiling as String         // error: no viable `as`; isTemperature(String) is false
```

The short form always declares a type parameter. It reads as "a type that
satisfies `isTemperature`". The constraint lists every destination the body
can produce.

### `requires` clauses

A `requires` clause states any compile-time `Boolean` expression over the
compile-time parameters. It can relate several parameters, check value
parameters, or compare directly:

```zax
MyRange$(Low : type, High : type) requires canCompare(Low, High) :: type {
  low : Low
  high : High
}

MyRing$(capacity : Integer) requires capacity > 0 :: type {
  slots : Byte[capacity]
}

MyBits$(UseType : type) requires UseType == Boolean :: type {
  packed : Byte[2]
}
```

A value parameter always writes its kind, as `capacity : Integer` does, and
puts its check in `requires`. A reader never has to open a constraint function
to learn whether a parameter is a type or a value.

**Placement.** The clause comes after every parameter it can name:

- on a type, after the `$` list and before `::`;
- on a function or operator, after the complete prototype, among the trailing
  words before `=`. It can then name `$` parameters and type arguments alike:

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

The clause reaches as far as it can, to the `::` or `=` that ends it.

**What it may use.** Only compile-time inputs: types, `$` values, type
arguments, and `[<constant>]` inputs. It cannot use a runtime input, because it
runs before any call:

```zax
clamp$ final : (UseType : type)(result : UseType)(
  value : UseType,
  limit : UseType
) requires limit > 0 = {   // error: limit is a runtime input
}
```

**Combining.** Short-form constraints and a `requires` clause combine with
`&&`. Short forms are checked first, in parameter order, then the clause. The
check stops at the first `false`, so a clause may assume that the short forms
passed:

```zax
MyRange$(Low : isNumeric, High : type) requires canCompare(Low, High) :: type {
  // canCompare runs only when isNumeric(Low) is true
}
```

**Part of the prototype.** A forward declaration or a deferred body repeats the
clause, as it repeats other contract elements.

### What a failed constraint does

A `false` constraint removes that candidate, exactly as a type mismatch does.
If no candidate remains, the ordinary "no viable candidate" error names the
constraint that rejected each one:

```text
error: no viable `as` for Celsius as String
  candidate: Celsius.as(DestinationType : isTemperature)
    rejected: isTemperature(String) is false
```

There is no other way to remove a candidate. An error inside a generic body is
an error, never a silent fallback to another candidate. To remove a declaration
on some targets, select it at compile time with `if compile` and an
[`outward` declaration](declarations-and-bindings.md#outward-declarations).

### Layout queries in constraints

Every layout query names its environment: `active`, `target`, or `host`. A
constraint runs on the build machine but decides something about the shipped
program, so it almost always wants `target`:

```zax
isSmall final : (result : Boolean)(UseType : type) = {
  return target size of UseType <= 16
}

isSmallHere final : (result : Boolean)(UseType : type) = {
  return active size of UseType <= 16   // allowed, but in a constraint `active` means the build machine
}
```

The complete rule is in
[layout queries name their environment](compile-time-code.md#layout-queries-name-their-environment).

## Checking generic bodies

A `Boolean` constraint says whether arguments are accepted. It does not tell
the compiler which operations the body uses, so a generic body is checked once
for each instantiation:

```zax
supportsAdd final : (result : Boolean)(UseType : type) = {
  return wellformed{
    // A lambda's parameters need no construction; see below.
    check := [[]] (result : UseType)(a : UseType readonly &, b : UseType readonly &) {
      return a + b
    }
  }
}

addThenMultiply$ final : (UseType : supportsAdd)(result : UseType)(
  value1 : UseType,
  value2 : UseType
) = {
  return (value1 + value2) * value1   // also uses `*`
}

words := addThenMultiply("a", "b")
```

```text
error: no `*` for String * String
  in addThenMultiply$(String), line 6
  instantiated from: words := addThenMultiply("a", "b")
```

The constraint checked only `+`, so `String` was accepted and the body failed.
Listing every operation the body needs moves the error to selection, where it
reads as "rejected by the constraint". Either way it is an error.

Two consequences:

- An instantiation that is never used is never fully checked. A library can
  test its own instantiations with a compile-time assertion:

  ```zax
  compile{
    assert(wellformed{ addThenMultiply(1, 2) })
  }
  ```

- [`wellformed{...}`](compile-time-code.md#checking-whether-code-is-well-formed-wellformed)
  checks a generic instantiation its payload names in full, body included.

### Checking operations without constructing values

Declaring a value inside a `wellformed` payload also checks that the type can
be default-constructed, which a type supporting `+` may not be:

```zax
needsDefault final : (result : Boolean)(UseType : type) = {
  return wellformed{
    a : UseType      // also requires default construction
    b : UseType
    c := a + b
  }
}
```

A lambda's parameters are declared, not constructed. Declaring a lambda inside
the payload checks only the operations, and states which qualifications they
must accept:

```zax
supportsArithmetic final : (result : Boolean)(UseType : type) = {
  return wellformed{
    check := [[]] (result : UseType)(a : UseType readonly &, b : UseType readonly &) {
      return (a + b) * a
    }
  }
}
```

## Specializations

A generic may have several declarations under one name. Different arguments
may need entirely different implementations:

```zax
MyBits$(UseType : type) :: type {
  values : UseType[16]
}

MyBits$(UseType : type) requires UseType == Boolean :: type {
  packed : Byte[2]
}

flags : MyBits$(Boolean)   // the second declaration: 16 flags in 2 bytes
counts : MyBits$(U8)       // only the first is viable
```

### Which declaration wins

Each declaration's constraint is a set of **terms**. Each short-form constraint
is one term, and each `&&`-separated part of a `requires` clause is one term. A
part joined with `||`, or a negation, counts as one term.

> Declaration A beats declaration B when A has every term B has, written
> identically and resolving to the same declarations, plus at least one more.
> Otherwise, when both are viable, the use is ambiguous.

The compiler compares how constraints are written, never what they compute, so
the answer is always decidable. Plain `type` has no terms, so any constraint
beats it.

A refinement wins:

```zax
Fmt$(Label : isText, Amount : type) :: type { }        // terms: isText(Label)
Fmt$(Label : isText, Amount : isNumeric) :: type { }   // terms: isText(Label), isNumeric(Amount)

a : Fmt$(String, Integer)   // the second wins: it has the first's term and one more
```

Different checks never make a winner, even when one seems narrower:

```zax
Fmt$(Label : isString, Amount : isNumeric) :: type { }   // terms: isString(Label), isNumeric(Amount)
Fmt$(Label : isText, Amount : type) :: type { }          // terms: isText(Label)

b : Fmt$(String, Integer)
// error: ambiguous; isString(Label) and isText(Label) are different terms
```

To make one declaration a refinement of another, repeat the shared check
identically. To make two declarations exclusive, negate one's constraint in the
other:

```zax
isCompact final : (result : Boolean)(UseType : type) = {
  return target size of UseType <= 16
}

MyStore$(UseType : isCompact) :: type {
  inline : UseType
}

MyStore$(UseType : type) requires !isCompact(UseType) :: type {
  boxed : UseType * strong
}
```

### Generic and non-generic candidates

A generic `bar$` and a non-generic `bar` share one name and one set of
candidates. A call without `$` considers both; a call with `$` considers only
generic candidates:

```zax
bar final : (result : Integer)(value : Integer) = { }
bar$ final : (UseType : type)(result : UseType)(value : UseType) = { }

myInteger : Integer = 3
myByte : U8 = 3

bar(myInteger)            // both exact: the non-generic wins
bar(myByte)               // the non-generic needs a conversion; the generic is exact: the generic wins
bar$(Integer)(myInteger)  // only the generic is a candidate
```

Ordinary [preference](function-invocation.md#partial-order-preference) decides
match quality first. Only between equally good matches does a non-generic
candidate beat a generic one; then the term rule ranks generic declarations.
Anything still tied is ambiguous. Adding a convenient non-generic overload
therefore never captures calls that were exact matches for the generic.

### Specializations may differ in members

A specialization is a different declaration. It may have different members:

```zax
firstOf$ final : (UseType : type)(result : UseType)(bits : MyBits$(UseType) readonly &) = {
  return bits.values[0]
}

firstOf(flags)   // error inside firstOf$(Boolean): MyBits$(Boolean) has no `values`
```

Code written against the general declaration fails for specializations that
lack what it uses. Give specializations a common surface when generic code
should treat them alike. C++ programmers know this as the `vector<bool>`
problem.

## How names inside a generic body are found

A library generic often calls a helper that each new type should be able to
supply:

```zax
// Library
normalize final : ()(value : Integer) = { ... }

process$ final : (UseType : type)()(value : UseType) = {
  normalize(value)
}

// Module Readings, written later
MyReading :: type { ... }
normalize final : ()(value : MyReading) = { ... }

// Any module that sees Readings
process(myReading)   // calls the Readings normalize
```

The rule:

1. **Names that do not involve a parameter** resolve where the generic is
   defined, under ordinary source order, once. `helper(5)` in a generic body
   always means the same function.
2. **Calls whose arguments involve a parameter**, like `normalize(value)`, also
   search the modules that own the argument types and the modules visible
   where the generic is used, including declarations made after the generic.
3. **Ordinary selection** ranks the combined candidates. An exact
   `normalize(MyReading)` beats the library's `normalize(Integer)` reached by a
   conversion; equally good candidates are ambiguous.

Without step 2, `process(myReading)` would fail, or silently call the library's
`normalize(Integer)` through a conversion.

Operations owned by the argument's type, such as a `+` declared on
`MyReading`, are found through the type as usual. A new type does not have to
add members to work with a generic: free functions in its module, or in any
module visible at the use, work too.

Every use of the same instantiation must bind the same way. If two uses would
bind `normalize(value)` differently, because only one of them can see an extra
`normalize`, the program is rejected, as described next.

## Extending generics from other modules

Any module may add a specialization of a generic, or a helper a generic body
can find. That includes a module that owns neither the generic nor the type:

```zax
// Module Bits
MyBits$(UseType : type) :: type { values : UseType[16] }

// Module Tweaks: owns neither MyBits$ nor ThirdPartyType
MyBits$(UseType : type) requires UseType == ThirdPartyType :: type { packed : Byte[2] }
normalize final : ()(value : ThirdPartyType) = { ... }
```

Each use chooses from what it can see: the generic's definition, the modules
that own the argument types, and the modules visible where it is written. A
module that imports `Tweaks` sees its specialization and its `normalize`.

### Every use must agree

One instantiation is one type with one set of choices. Every use of the same
instantiation, anywhere in the program, must make the same choices: the same
declaration, and the same bindings for calls that involve a parameter.
Otherwise the program is rejected:

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

The diagnostic names the declaration that makes the difference and every use
that disagrees. Its content does not depend on which module the compiler
reached first.

This is the cost of open extension: two libraries can each build on their own
and fail together. The failure is always a compile error, reported at the
latest when the whole program is assembled; it is never a silent mismatch.

An extender can also wrap the type in an [identity type](identity-types.md) of
their own and extend that. Extensions written by others for the original type
then cannot conflict with theirs; a conflict can only arise if another module
deliberately extends the new identity type.

### Later declarations in the same module

An instantiation is formed the first time it is used, from the declarations
visible at that point, and is then reused. A later declaration that would
change one of its choices is an error:

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

The fix is to declare the helper before the first use. The same applies to a
specialization declared after a use it would have won, and to a helper that
would change a `requires` result:

```zax
// Library
isPacked final : (result : Boolean)(UseType : type) = {
  return false
}

MyPackedStore$(UseType : type) requires isPacked(UseType) :: type { packed : Byte[2] }
MyPackedStore$(UseType : type) :: type { boxed : UseType * strong }

// Module Readings
early : MyPackedStore$(MyReading)   // isPacked(MyReading) is the library's: false

isPacked final : (result : Boolean)(UseType : type) requires UseType == MyReading = {
  return true
}
// error: this changes what isPacked(UseType) binds to in the `requires` that
// already selected MyPackedStore$(MyReading)
```

The error is reported by the time the module is finalized, and may be reported
earlier, for example when the instantiation is used again. A declaration
change between modules is the disagreement error above.

### `requires` is evaluated once

A `requires` clause is evaluated once for each argument set, and its answer is
kept:

- a `requires` may have effects on the build machine; they happen exactly
  once, on every compiler;
- the kept answer stays valid only while the choices it depended on hold; a
  later declaration that changes one is an error, never a silent
  re-evaluation; and
- calls in a `requires` that do not involve a parameter follow ordinary source
  order, like any code.

How a compiler detects a changed choice is its own business. Kept answers are
reused and compile points never re-run, so the method cannot change what the
program observes.

## Processing and compile points

A function with compile-time inputs is processed once per distinct input set
([two forms of generic](#when-code-is-processed-once-per-input)). Compile
points in its body run once per processing:

```zax
myInstantiationCount : Integer = 0

record$ final : (UseType : type)()(value : UseType) = {
  compile{
    ++myInstantiationCount   // once for each distinct UseType, in first-use order
  }
}
```

- **Order.** Compile points in a generic body run in the order their inputs
  are first used. If they write host-world globals, the results depend on that
  order, as for any [compile-time state](compile-time-code.md#the-host-worlds-state).
- **Module instance.** A compile point in a generic body runs in the host world
  of the module instance that defines the generic, not the module that uses
  it.
- **Host and target builds.** Zax builds a target program and a host world
  from one source. An instantiation over an environment-relative type, such as
  `MyTable$(Integer)`, has one identity and one set of choices in both, and is
  laid out by each build's own definition of `Integer`. Choices agree because
  constraints and `requires` clauses are evaluated once, during compilation,
  and every layout query in them names the environment it asks about.
- **Merging.** After processing, the compiler may merge instantiations whose
  generated code is identical. That never merges their identities.

## Limits

A generic that instantiates itself with ever-growing arguments never ends:

```zax
deepen$ final : (UseType : type)()(value : UseType) = {
  next : UseType[2]
  deepen(next)   // deepen$(UseType[2]), then deepen$(UseType[2][2]), ...
}
```

Each step is a new, finite type, so only a depth limit can catch it. The
`[<instantiation-depth=...>]` directive sets the maximum nesting of one
declaration's own instantiations:

```zax
deepen$ final [<instantiation-depth=8192>] : (UseType : type)()(value : UseType) = {
}
```

The compiler-wide default is `4096`, and the language guarantees at least that
depth. Exceeding it is a diagnostic showing the growing chain.

## Costs

Programmers must be able to discover:

- each distinct instantiation compiled, including one per qualification or
  reference shape of a worked-out type;
- generated code added for each instantiation that is not merged;
- compile-time work in constraints, `requires` clauses, defaults, and compile
  points in generic bodies, once per argument set or processing; and
- program-wide agreement checks when extensions are visible in some modules
  and not others.

## Diagnostics

Diagnostics should distinguish:

- a candidate rejected by a constraint, naming the constraint and the
  arguments;
- a candidate not viable because a shared parameter cannot be worked out to
  one type;
- an error inside a generic body, with the instantiation chain;
- an ambiguity between specializations, naming the terms each has;
- a runtime input used in `requires`;
- a layout query that does not name `active`, `target`, or `host`;
- a later declaration changing a choice already made, naming both;
- uses of one instantiation that choose differently, naming the deciding
  declaration and every disagreeing use;
- a generic type used without `$(...)`;
- a non-compile-time argument for `[<constant>]` or a `$` value parameter; and
- an exceeded instantiation depth, with the chain.

## Source stability

The following changes are source- or behavior-visible:

- adding a specialization or helper after a use it would change is an error
  in that module;
- adding a specialization or helper in a module that only some users import
  can make combined programs fail to agree;
- adding a non-generic overload changes only calls where it is an equally good
  match;
- adding, removing, or respelling a constraint term changes ranking;
- tightening a constraint can make existing uses not viable; and
- changing a qualifier or reference on an argument creates a different
  instantiation.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar or a compiler
architecture.

Future work owns:

- generic aliases for families and partial application, and generic forwards;
- generic enums and generic partials;
- explicit qualifier-parameterized families;
- a standard set of protocol constraints, such as for cursors, arrays, and
  allocation policies, and any whole-type contract facility;
- a dedicated form for specializing one exact type;
- whether a non-`final` function may take `[<constant>]` inputs, given that
  such a function has no runtime function value to install in a replaceable
  slot;
- whether a name in a generic body that does not involve a parameter, and
  cannot be resolved, is reported when the generic is never used;
- type reflection operations, including removing qualifiers and references;
- the exact spelling of compile-time error reporting;
- the integer factory and other numeric factory interfaces; and
- exact diagnostic identifiers and presentation.

For Zax's accepted foundational direction, see the
[language vision](vision.md).
