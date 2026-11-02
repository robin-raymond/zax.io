# Zax compile-time code

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers who want the compiler to compute values, choose code, or check code while compiling |
| Applies To | Compile-time execution of ordinary Zax code, compile-time values, execution policies, environments during compilation, values crossing into the program, compile-time selection, well-formedness checks, and compilation information; not a formal grammar, a compiler architecture, or a sandbox specification |
| Implementation State | Not established by this repository |
| Owns | The two-program mental model; `compile`, `compile{...}`, and `runtime`; when compile points run; what counts as a compile-time value; `[<execute=...>]` policies, including function values and replacement compatibility; which environment's definitions apply during compilation; conversion of values crossing into the program; compile-time state, ordering, and the compile-time `___`; `if compile` and `switch compile`; `wellformed{...}`; the `Compilation` namespace |
| Does Not Own | Directive syntax and attachment ([compiler directives](compiler-directives.md)); `outward` declarations ([declarations and bindings](declarations-and-bindings.md#outward-declarations)); environment-relative scalar names ([integers](integers.md#native-representation-and-software-emulation), [endianness](endianness.md)); literal declarations ([literal source and operators](literal-source-and-operators.md)); hook points ([partials](partials.md#asking-at-compile-time-whether-a-hook-is-fulfilled)); the runtime execution context ([execution context](execution-context.md)); generics |
| Source / Provenance | Legacy compiler-directive, meta-function, and compile-time execution input, reconciled with current literal, scalar, environment, partial, module, and invocation design |

## Work the compiler can do for you

Some values are known before the program ever runs. Zax lets you compute them
with ordinary Zax code while compiling:

```zax
cubed final : (result : Integer)(value : Integer) = {
  return value * value * value
}

myTableSize := compile cubed(4)
// The compiler runs cubed(4) while compiling.
// The program contains the constant 64; no call to cubed happens at runtime.
```

`compile` asks for the expression after it to be evaluated during compilation.
The function is ordinary Zax. There is no separate macro language and no
textual preprocessing: the same `cubed` can also be called at runtime.

The rest of this page builds on four ideas:

- **Compile-time code is ordinary code.** Anything you can write, the compiler
  can run, as long as it does not need values that exist only at runtime.
- **Your source is built twice.** One build is the program you ship. The other
  runs on the build machine while compiling.
- **Only plain values cross into the program.** Compile-time work affects the
  shipped program through the constants it produces.
- **Timing never changes meaning by accident.** Code moves to compile time only
  when you or the called function ask for it, or when nothing could observe the
  difference.

## Two programs from one source

When Zax compiles your source, it effectively builds two programs from it:

- the **target program**, the program you ship and run; and
- the **host world**, the same code compiled for the build machine, which runs
  pieces of itself while the compiler works.

The two are separate. Each has its own copy of every global variable, its own
`___` [execution context](execution-context.md), and its own memory. Code that
runs at compile time runs in the host world, so its effects happen on the build
machine, during the build:

```zax
myBuildCount : Integer = 0

compile{
  ++myBuildCount                       // the host world's copy becomes 1
  writeBuildLog("compiling renderer")  // written on the build machine, during the build
}

main final : ()() = {
  print(myBuildCount)                  // prints 0: the shipped program has its own copy
}
```

Effects on the build machine are legitimate. Compile-time code may read files,
build lookup tables, or write logs. They are simply a different program's
effects from the ones the shipped program performs when it runs.

## Asking for compile time

### `compile` on an expression

`compile` covers the rest of the complete expression it starts. It stops at an
enclosing closing parenthesis, a comma, the end of the statement, or, in
`if compile` and `switch compile`, the opening brace of the body:

```zax
myLimit := compile cubed(4) + 10        // cubed(4) + 10 is computed while compiling
log(compile cubed(3), userCount)        // stops at the comma: only cubed(3)
myTotal := (compile cubed(4)) + offset  // parentheses narrow it to cubed(4)
```

Because `compile` reaches as far as it can, a runtime value caught inside it is
an error rather than a silent split between compile time and runtime:

```zax
userValue := readNumber()
myScaled := compile cubed(4) + userValue // error: userValue exists only at runtime
```

The result keeps the type the expression has. `compile` changes when the work
happens, not what it produces. See
[integer literals](integer-literals.md#concrete-results-and-compile-time-execution)
for how concrete results keep their types.

### `compile{...}` for statements

To run statements at compile time, use the block form. Its brace is attached:
`compile{` with no space.

```zax
compile{
  loadPalette("palette.png")   // fills a host-world global
}

myBackground := compile paletteEntry(0)  // a later compile expression reads that global
```

`compile{...}` produces no value. From the target program's point of view,
nothing happens where it is written. Its braces form an ordinary block, so
declarations inside it are local to it. Its work reaches the program only
through later `compile` expressions that read the host world's globals.

### Nesting

A `compile` inside an expression that is already compile time changes nothing.
It is legal; a lint may point out that it is redundant:

```zax
myResult := compile cubed(2) * compile cubed(3) // the inner compile is redundant
```

The opposite direction is not possible: compile-time code cannot ask for
runtime values, as described next.

## When compile points run

A `compile` expression or block is a **compile point**. Each one runs when the
compiler compiles the place where it is written:

- **Once, not per execution.** A compile point inside a loop runs once, while
  that loop is compiled.
- **Whether or not runtime reaches it.** A compile point in a function that
  nothing calls still runs, and if it fails, the build fails. Mistakes in
  unused code stay visible.
- **Never by running the program.** The host world does not start `main` and
  collect whatever compile points it happens to reach.

```zax
retry final : ()() = {
  attempt := 0
  while attempt < 3 {
    myDelay := compile cubed(5)  // runs once, during compilation, not three times
    sleepFor(myDelay)
    ++attempt
  }
}
```

Names inside a compile point refer to the host world. A global named there is
the host world's copy. A local runtime variable cannot be used at all, because
its value does not exist yet:

```zax
handleRequest final : ()() = {
  request := readRequest()
  mySize := compile measure(request)   // error: request is a runtime value
}
```

There are two exceptions to "every compile point runs": code that
[compile-time selection](#choosing-code-at-compile-time) discards, and code
inside a [`wellformed{...}`](#checking-whether-code-is-well-formed-wellformed)
check, which is checked but never run.

## Compile-time values

Several features need to know whether a value is available at compile time.
The rule is the same for all of them. A **compile-time value** is:

- a literal;
- an expression built only from compile-time values;
- the result of a compile point;
- the result of calling a function whose
  [execution policy](#letting-a-function-decide-execution-policies) is
  `compile`, or `folding` with every argument a compile-time value;
- a [`Compilation`](#compilation-information) query, `size of`, or another
  compile-time query; or
- a `final` binding, local or global, initialized from a compile-time value.

Inside compile-time code, every global is the host world's copy, so reading any
global there gives a compile-time value.

```zax
main final : ()() = {
  myColumns final := 16    // a compile-time value: final, with a constant initializer
  myRows := 16             // a runtime variable: it may be reassigned later

  if compile myColumns > 8 {   // fine
    useWideLayout()
  }

  if compile myRows > 8 {      // error: myRows is a varying runtime variable
    useWideLayout()
  }
}
```

A varying local is a runtime variable even when its initializer is a constant,
because later code may assign to it.

The `is constant` query asks this question directly: `myColumns is constant` is
`true` here, and `myRows is constant` is `false`. It answers by exactly these
rules, never by whether the optimizer happened to pre-compute something.

## Letting a function decide: execution policies

A caller can always write `compile`. A function's author can also declare when
the function runs, with an execution policy written after the declaration's
name-side words, such as `final`:

```zax
hashName final [<execute=folding>] : (result : U64)(name : String readonly &) = {
}

buildSineTable final [<execute=compile>] : (result : Integer[])(size : Integer) = {
}

readPort final [<execute=runtime>] : (result : Byte)(port : U16) = {
}
```

There are four policies:

| Policy | When the function runs | Is the result a compile-time value? |
| --- | --- | --- |
| `auto` (the default) | At runtime, unless a caller writes `compile`. The compiler may still pre-compute work whose difference nothing could observe. | No |
| `runtime` | Never at compile time. | No |
| `folding` | At compile time whenever every argument is a compile-time value, otherwise at runtime. | Yes, when it runs at compile time |
| `compile` | Always at compile time. It has no runtime form at all. | Yes |

```zax
main final : ()() = {
  myKey := hashName("player")     // folding, constant argument: computed while compiling
  userKey := hashName(readLine()) // folding, runtime argument: computed at runtime
  mySine := buildSineTable(256)   // compile: always while compiling
  myTotal := sumTo(1000)          // auto: an ordinary runtime call
  myPort := compile readPort(1)   // error: readPort is runtime-only
}
```

### `auto` stays invisible

An **observable effect** is anything visible outside the computation itself,
such as output, file or device access, the clock, or state that outlives the
call. Under `auto`, the compiler may pre-compute work that has no observable
effect, as an optimization. Because that choice is invisible:

- an `auto` result is never a compile-time value, so `is constant`,
  compile-time selection, and other compile-time rules never depend on
  optimizer choices; and
- a failure in pre-computed `auto` work is still a runtime failure. The
  compiler may warn, but it does not reject the program:

```zax
myRatio := divide(10, 0)  // auto: still a runtime panic, even if pre-computed
```

Under `folding` and `compile`, a panic during compile-time execution is a
compiler diagnostic instead, naming the operation and where it happened.

### Why `folding` is not the default

A function with no parameters always has "every argument constant". If
`folding` were the default, `main()`, `now()`, and `readConfig()` would all run
while compiling and bake build-machine results into the program. `folding` is
a promise a function's author makes deliberately, typically for pure
computations like hashing.

### Writes under `folding`, and the `runtime` keyword

A `folding` function may update global state, such as a cache, a lock, or a
counter. Which world's copy it updates follows from where the call runs:

```zax
myCallCount : Integer = 0

record final [<execute=folding>] : ()(value : Integer) = {
  ++myCallCount
}

record(3)           // folds: updates the host world's myCallCount
record(userValue)   // runs at runtime: updates the program's myCallCount
```

This is predictable, but it acts at a distance:

```zax
myLimit final := 64
record(myLimit)     // folds, because myLimit is a compile-time value
```

If someone later makes `myLimit` configurable at runtime, that call's write
silently moves to the running program. A lint may flag `folding` functions that
write globals.

When a call must run in the target program, write `runtime`. Like `compile`,
it covers the rest of the expression:

```zax
runtime record(3)                         // runs in the program, even with a constant argument
myTotal := runtime hashName("a") + hashName("b") // both hashes run at runtime
```

`compile` may appear inside `runtime` to pre-compute one piece. `runtime` may
not appear inside compile-time code, and it cannot be applied to a `compile`
function, which has no runtime form:

```zax
runtime log(compile cubed(3))          // fine: only cubed(3) is pre-computed
myValue := compile check(runtime readLine()) // error: compile-time code cannot reach runtime
runtime buildSineTable(256)            // error: buildSineTable exists only at compile time
```

`runtime` is a keyword only where an expression follows it, so a variable
named `runtime` remains possible.

### Call chains are checked

A compile-time request covers everything the call reaches. If anything along
the way is `runtime`-only, the compiler reports the whole chain:

```text
error: cannot run at compile time
  makeTable() calls checksum() calls readPort(), which is runtime-only
```

### Function values and replaceable implementations

A function's policy is part of its visible prototype. That matters when
functions are stored and called through values:

- A function value known at compile time, such as a named `final` function,
  keeps its policy when called through the value.
- A call through a runtime slot is a runtime call.
- A `compile` function has no runtime form, so it has no runtime function
  value. Taking one is legal only inside compile-time code.
- A lambda uses the same policy slot and defaults to `auto`. A lambda created
  at compile time can cross into the program only if it captures nothing.

A replaceable function slot (a non-`final` function declaration) has a policy
too, written after its name. Anything installed in it must be able to run
everywhere the slot can be called from:

| Slot policy | Installable functions |
| --- | --- |
| `auto` or `runtime` | `auto`, `runtime`, `folding` |
| `folding` | `folding` |
| `compile` | `compile`, `folding`, `auto` |

```zax
readSensorScaled final [<execute=runtime>] : (result : Integer)(value : Integer) = {
  return readSensor() * value
}

myHandler : (result : Integer)(value : Integer) = cubed   // an auto slot
myHandler = readSensorScaled                               // fine: runtime-only works in an auto slot

myBuilder [<execute=compile>] : (result : Integer)(value : Integer) = cubed
myBuilder = readSensorScaled                               // error: cannot run at compile time
```

A call through a `compile` slot always runs at compile time and gives a
compile-time value, whichever function is installed.

Complete function-value and lambda behavior is defined by
[function invocation](function-invocation.md) and
[lambdas and callable composition](lambdas-and-callable-composition.md).

## Which definitions apply while compiling

Some scalar types have environment-relative definitions. `Integer`, for
example, may be 64 bits on the build machine and 32 bits on the target, and
byte order may differ too. The **active environment** is the one whose
definitions apply to the code being run:

- In the target program, the active environment is the target.
- In compile-time code, the active environment is the **compiler host**, the
  machine running the compiler.

So compile-time code uses the build machine's definitions unless it asks for
the target's. Ask by naming the environment explicitly:

```zax
myHostWidth := compile size of Integer              // the build machine's Integer
myTargetWidth := compile size of Target.Integer     // the target's Integer
myHostWord := compile size of CompilerHost.Integer  // the build machine's, explicitly
```

Code that computes facts about the target, such as table layouts, should name
the target's types. The environment-relative routes are defined by
[integers](integers.md#native-representation-and-software-emulation) and
[endianness](endianness.md#active-and-absolute-names).

Compile-time code may use the build machine's hardware, but hardware must never
change a result. Running an operation in hardware or in software must give the
same value.

## Values crossing into the program

A compile-time result crosses into the target program as a constant. Values
cross by copy: an array or string crosses as its contents, which the program
holds as constant data. Pointers, references, and lambdas that capture state
never cross, because they would point into the build machine's memory.

A crossing value keeps its **type identity**. If that type's definition differs
between the two environments, the value is converted to the target's
definition, without silently losing data:

```zax
myCount : Integer = compile countEntries()
// Integer means the same type in both worlds, but the build machine's Integer
// may be wider. The value is checked against the target's Integer range.
```

| Value | What happens when it crosses |
| --- | --- |
| Integers | Checked against the target definition's range. A value out of range is a compiler error. |
| Reals out of range | A value beyond the target format's finite range is a compiler error. |
| Reals in range | Rounded correctly to the nearest value in the target format. |
| Special real values | NaN payloads, signalling NaN, and negative zero follow the target format's rules. A payload the target cannot represent is a compiler error. |
| Byte order | Converted. It changes representation, not value. |
| Composite values | Converted member by member. |
| Values whose type's target layout is still pending | Cannot cross until that layout is final. See [compile-time state](#the-host-worlds-state). |

Rounding a real is not a warning by default, because nearly every narrowing of a
real rounds. It does have a consequence worth knowing. A value computed at
compile time with the build machine's wider `Float`, then rounded once, can
differ in its last bits from the same computation done at runtime in the
target's `Float`:

```zax
myRadius final := 2.5
myArea : Float = compile computeArea(myRadius) // build machine's Float, then rounded
myOtherArea : Float = computeArea(myRadius)    // target Float throughout; may differ slightly
```

More generally, a compile-time result can depend on the build machine whenever
the code uses an environment-relative type whose definition differs between
the two environments. A smaller build machine may overflow where the target
would not; that overflow is a compile-time panic and becomes a compiler
diagnostic, never a wrong value. To make results independent of the build
machine, compute with exact types such as `U32`, or with explicit target types
such as `Target.Integer`, whose definitions are the same in both worlds:

```zax
countEntries final : (result : Target.Integer)() = {
  total : Target.Integer = 0   // the target's Integer, even while compiling
  // ...
  return total
}

myCount := compile countEntries()   // identical on every build machine
```

## The host world's state

### One host world per module instance

Each [module instance](namespaces-and-modules.md#generative-imports-and-injection)
has its own host world. Its globals persist from one compile-time evaluation to
the next, in source order. A compile-time call into an imported module uses that
module instance's host world.

A global is initialized in the host world the first time compile-time code
reaches it. Its own initializer runs first, and may reach other globals in turn.
A cycle is a diagnostic:

```zax
myTableSize : Integer = 256
myTable : Integer[] = buildSineTable(myTableSize)

myChecksum := compile sum(myTable)
// The host world initializes myTableSize, then myTable, then runs sum.
```

The target program initializes its globals in source order, so the two worlds
may initialize in different orders. That only matters when initializers depend
on each other's effects.

### Compile-time writes and order

Any compile-time code may write host-world globals, including literal bodies
and `folding` calls. Because evaluations happen in source order, the results are
defined:

```zax
mySeed : U64 = 0x9E3779B9

MyRandom :: type {
  operator literal 'rand' final : (result : U64)(
    payload : String immutable readonly final &
  ) unbound = {
    mySeed = mix(mySeed, payload)   // a host-world write from a literal body
    return mySeed
  }
}

first := MyRandom.rand'x'
second := MyRandom.rand'x'   // differs from first; swapping the two lines swaps the values
```

Order has practical costs:

- changing an early evaluation that touches shared state forces every later one
  that depends on that state to be recomputed;
- moving code between files of a module can reorder those evaluations; and
- a compiler may evaluate in parallel, but must evaluate in order wherever
  evaluations share host-world state.

### The compile-time `___`

The host world has its own [execution context](execution-context.md). Modules
can add members to `ExecutionContext` through partials, and the target's
context shape is fixed only when the whole program is assembled. Compile-time
code cannot wait for that, so the host world's context is fixed at each compile
point instead. It contains:

- the language's core members;
- partials from completed imports; and
- this module's partials completed before the compile point, in source order.

Between compile points the shape only grows. Existing members keep their
values, and new members start at their declared defaults:

```zax
compile{
  warmCaches()          // the host ___ has only the core members here
}

MyTracking :: partial ExecutionContext {
  requestId : U64 = 0
}

compile{
  Module.MyTracking :: expose partial
  ++___.requestId       // the shape now includes requestId, which started at 0
}
```

A compile point can use a partial's members only after granting it, as for any
[partial](partials.md#making-a-partial-visible). Granting a partial whose
declaration comes after the compile point is an error at that point.

Reflection describes the same `ExecutionContext` identity in both worlds. In
compile-time code, plain reflection describes the host world's current shape.
A question about the target's final shape waits until that shape is fixed, or
is rejected before then. The same applies to `size of` on any type whose target
layout is still pending, as described by
[partials](partials.md#storage-from-any-module).

## Choosing code at compile time

### `if compile` and `switch compile`

Writing `compile` on a condition makes the `if` a compile-time selection. Only
the selected branch becomes part of the program:

```zax
if compile size of Target.Integer > 4 {
  useWideTables()
} else {
  useNarrowTables()
}
```

The same works for `switch`:

```zax
myRenderer final := 2

switch compile myRenderer {
  case 1
    useSoftwareRenderer()
  case 2
    useHardwareRenderer()
}
```

The condition or switch value must be a compile-time value, and case tests are
compile-time values too. If the selected body enters another case's body with
`goto`, that body is kept as well.

An ordinary `if` whose condition happens to be constant is not a compile-time
selection. Both of its branches must still be valid code.

### Discarded branches

An unselected branch is discarded. It must contain correctly formed tokens with
balanced delimiters, but nothing in it is parsed further, resolved, or
layout-checked. It may therefore name things that do not exist for this target:

```zax
if compile Compilation.target.hasFeature("avx512") {
  useWideRegisters(zmm0)   // never resolved for targets without the feature
} else {
  useScalarPath()
}
```

Two consequences follow:

- a mistake in a rarely selected branch is found only when some target selects
  it, which an optional lint can help with; and
- formatters treat a discarded branch as opaque text.

### Selecting declarations

`if compile` may also appear where an ordinary `if` cannot, such as in a type
body or namespace body. Its branches still form scopes. To make a declaration
visible after the construct, mark it
[`outward`](declarations-and-bindings.md#outward-declarations):

```zax
if compile size of Target.Integer > 4 {
  MyIndex outward :: alias type U64
} else {
  MyIndex outward :: alias type U32
}

myPosition : MyIndex   // valid whichever branch was selected
```

Code that uses a declaration found in only one branch must itself be selected,
or go through a name every branch provides, like `MyIndex` above.

## Checking whether code is well formed: `wellformed{...}`

Sometimes the question is not "what is this value?" but "would this code
compile here?". `wellformed{...}` answers with a compile-time `Boolean`:

```zax
myValue := readSensor()

if wellformed{ ++myValue; myValue *= 3 } {
  scaleUp(myValue)   // the payload was checked, never run: myValue is unchanged
}
```

The payload is checked as if it were the body of a function written at that
point, able to see the surrounding names. It is never executed. Names it uses
have types but no values, and its declarations never escape.

A `wellformed` answer is a compile-time value, so it can drive
[compile-time selection](#choosing-code-at-compile-time). A typical use is a
fallback when an optional facility is absent:

```zax
if compile !wellformed{ Module.UseComplexMath } {
  UseComplexMath outward :: import Module.DefaultComplexMath
}
```

`wellformed` is a keyword only with its attached brace, `wellformed{`, so a
variable named `wellformed` remains possible.

### How deep the check goes

The payload is checked completely against everything it can see: names,
operator and overload selection, conversions, qualifiers, and the prototypes of
the functions it calls. Code that already exists is trusted to be checked on
its own:

```zax
increment final : ()(value : MyCounter writable &) = {
  value.cuont += 1     // a typo: reported as an error in increment itself
}

ok := wellformed{ increment(myCounter) }   // true: the call matches the prototype
```

Code the payload itself creates is checked in full. That includes a generic
instantiation the payload causes; the details belong to future generic design.

### Warnings and intent

A payload may compile but produce a language-defined warning, such as a
deprecation. `wellformed` does not guess whether that counts. If a warning
occurs and nothing says how to treat it, the `wellformed` itself is an error.
Options in angle brackets decide, one category at a time:

```zax
if wellformed{ fastPath(myData) } { ... }                   // error if fastPath is deprecated
if wellformed<allow-deprecated>{ fastPath(myData) } { ... } // deprecation counts as well formed
if wellformed<reject-deprecated>{ fastPath(myData) } { ... }// deprecation counts as failure
```

Without this rule, a library that merely deprecates `fastPath` would silently
switch every such caller onto its fallback branch. Lints never affect
`wellformed`; only warnings the language defines do. Deprecation is the only
such category today.

An [intent error](intent-acknowledgements.md) is an error, so a payload needing
an acknowledgement must contain it:

```zax
ok := wellformed{ first <+> second }                                     // false if this join needs acknowledgement
ok := wellformed{ intent<same-prefix-literal-join>{ first <+> second } } // checks the acknowledged form
```

### Seeing why a check failed

A `false` hides its reason. `explain` keeps the result but reports the
payload's diagnostics as compiler notes:

```zax
if wellformed<explain>{ fastPath(myData) } {
  // note: fastPath expects MyBuffer readonly &, found MyBuffer
  fastPath(myData)
}
```

Options combine: `wellformed<reject-deprecated, explain>{...}`. A compiler
option may also explain every `wellformed` failure without source edits.

### Checking has no effects

Checking a payload changes nothing: no host-world state, no declarations, and
no later results. A compile point inside a payload is checked but not run.

To require that some code compiles, assert it at compile time. A failed
compile-time assertion is a compiler diagnostic:

```zax
compile{
  assert(wellformed{ ++mySample })
}
```

## Compilation information

The language-provided `Compilation` namespace answers questions about the build.
Like `Integer` and `Scalars`, it is protected: no declaration can shadow or
replace it. Every answer is a compile-time value, so no `compile` keyword is
needed:

```zax
myVersion := Compilation.compiler.version.major
mySse := Compilation.target.hasFeature("sse")
myProfile := Compilation.target.setting("PROFILE")   // build configuration for the target
myHere := Compilation.here                            // this source location
```

| Member | Type | Meaning |
| --- | --- | --- |
| `here` | `SourceLocation` | Where the query is written |
| `caller` | `SourceLocation` | The call site, in a default argument only |
| `compiler` | `CompilerInfo` | The compiler's `name`, `vendor`, and `version` |
| `target` | `Environment` | Facts about the target environment |
| `compilerHost` | `Environment` | Facts about the build machine |
| `build` | `BuildInfo` | Facts about this build |
| `module` | `ModuleInfo` | Facts about the current module instance |
| `file` | `FileInfo` | Facts about the current source file |

| Type | Members |
| --- | --- |
| `SourceLocation` | `file`, `line`, `column`, `function`, `module` |
| `Version` | `major`, `minor`, `patch`, `preRelease`, `build`, `text` |
| `Environment` | `name`, `architecture`, `os`, `endianness`, `hasFeature(name)`, `setting(name)` |
| `BuildInfo` | `configuration`, `time` (`unix`, `nt`, `text`), `variable(name)` |
| `ModuleInfo` | `name`, `location`, `path`, `importVersion`, `hash`, `git` (`tag`, `branch`, `commit`) |
| `FileInfo` | `path` (relative to the module root), `name`, `hash`, `generator`, `modified` |

`setting(name)` reads a value from the build configuration. `variable(name)`
reads an operating-system environment variable on the build machine. Some
members may be absent, such as `setting`, `variable`, `importVersion`, `git`,
and `generator`; their results are optional values.

The target machine's own environment variables are not here. They exist only
when the program runs, so they come from an ordinary runtime library call.

Some members make a build depend on when or where it runs: `build.time`,
`build.variable`, `module.path`, `module.git`, and `file.modified`. Build
configuration can pin their values when builds must be reproducible.

### The caller's location

A logging or tracing helper usually wants the location of its caller, not its
own. `Compilation.caller` provides it, and is valid only as a default argument,
where it reports the call site:

```zax
trace final : ()(where : SourceLocation = Compilation.caller) = {
  print(where.file.path, where.line)
}

trace()   // reports this line
```

Everywhere else, use `Compilation.here`.

## Costs

Programmers must be able to discover:

- compile-time work performed by compile points, `folding` calls, `compile`
  functions, and literal bodies, which can make builds slow;
- compile-time memory and host-world state;
- constants and tables added to the program by compile-time results;
- `auto` pre-computation, which may grow or shrink the program but never changes
  its meaning; and
- evaluations that must run in order because they share host-world state.

The language sets no time limit on compile-time work. A compiler stays silent
while evaluation is quick, reports progress naming the function once a
compile-time evaluation runs long, and leaves the decision to stop to the build.
A stopped build shows the compile-time call stack.

## Diagnostics

Diagnostics should distinguish:

- a runtime value used in compile-time code, naming the value;
- a compile-time request that reaches a `runtime`-only function, naming the
  call chain;
- `runtime` inside compile-time code, or applied to a `compile` function;
- a compile-time panic, with the operation, location, operands, and
  compile-time call path;
- a crossing value out of the target's range, or not representable in its
  format;
- a pending target layout needed by a crossing value or query;
- a global initialization cycle in the host world;
- a partial granted at a compile point before its declaration;
- an `if compile` or `switch compile` whose condition is not a compile-time
  value;
- a `wellformed` warning left undecided; and
- `Compilation.caller` outside a default argument.

A redundant nested `compile` is a lint, not an error.

## Source stability

The following changes are source- or behavior-visible:

- changing a function's execution policy changes where its calls run and
  whether their results are compile-time values;
- making a value runtime-configurable can move `folding` calls, and their
  writes, from compile time to runtime;
- changing the order of compile-time evaluations that share host-world state
  changes their results;
- adding or removing a declaration can change a `wellformed` answer and the
  branch it selects;
- deprecating a declaration makes undecided `wellformed` checks that use it into
  errors; and
- changing a type from environment-relative to exact, or the reverse, changes
  whether compile-time results depend on the build machine.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, a sandbox
specification, or a compiler architecture.

Future work owns:

- source generation at compile time;
- compile-time parameters, meaning arguments whose values a function body uses
  while compiling, together with the rest of generics;
- a `pure` promise and any broader effect tracking;
- the complete reflection surface;
- the exact spelling for asking whether a hook point has fulfillments;
- complete member types and spellings in `Compilation`; and
- exact diagnostic identifiers and presentation.

For Zax's accepted foundational direction, see the
[language vision](vision.md).
