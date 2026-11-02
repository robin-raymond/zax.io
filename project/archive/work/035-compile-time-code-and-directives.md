# 035: Compile-time code and compiler directives

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `035` |
| Created | 2026-09-26 |
| Completed | 2026-11-01 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted compile-time code, compiler directives, declarations, core flow, switch, invocation, lambda, type-definition, union, safety, namespace, qualifier, literal, integer, operator-catalog, partial, execution-context, source-structure, and terms owners |

## Non-authority notice

This file is a collaborative working record. Existing documentation, candidate
syntax, examples, and later aligned findings remain non-authoritative until a
separately discussed, aligned, and explicitly authorized promotion incorporates
them into their lasting owners.

## Fixed initiating input

This section records the aligned information known when work item `035` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

What is Zax compile-time code, and how do compiler directives work as part of
it?

Compile-time code and compiler directives are intrinsically linked, so this
item treats them as one concern rather than reviewing directives separately.

### Motivating pressure

- **Generics depend on it.** Generic work needs a settled model of compile-time
  evaluation first.
- **Directives appear without a general model.** Current owners already use
  directive forms such as `[<nothing-instance-default=trap>]` and the
  compiler-directive enclosure, but no owner defines what a directive is, when
  it runs, or how directives are categorized.
- **A large unreviewed legacy page.** The root `compiler-directives.md` covers
  many directives, including `compile`, `compilation`, `compiles`, `execute`,
  `requires`, `concept`, `panic`, `inline`, `export`, literal directives, and
  qualifier-default directives.
- **Open compile-time questions from other owners.** These include detecting
  whether a hook point has fulfillments, `size of` before a type's storage
  closes, required compile-time execution for literals, and native versus
  compiler-host versus target execution.

### Starting input

The maintainer's notes are
[`project/raw/maintainer-notes/compiler-directives.md`](../raw/maintainer-notes/compiler-directives.md).
What those notes describe takes priority over the legacy material wherever they
differ. The maintainer fills the notes before this item is assigned.

The legacy root [`compiler-directives.md`](../../compiler-directives.md) and the
raw [compile-time execution input](../raw/compile-time-execution.md) are
evidence. They are not authoritative over the concept or the maintainer's notes.

### Starting boundaries

- Do not design generics. The maintainer's generics notes are future input and
  are not read in this item.
- Async-related directives, such as `synchronous` and `asynchronous`, are
  outside this item unless a concrete compile-time consequence makes them
  necessary.
- Panic categories, lint suppression, and unsafe controls stay with the
  [analysis-control input](../raw/analysis-controls.md) except where directive
  syntax touches them.

### Initial stopping guidance

Establish the foundational model first: what runs at compile time, how native,
compiler-host, and target execution relate, and the general form and categories
of directives. Then give each legacy directive a disposition (promote, defer, or
reject) rather than designing every directive in full.

Creating and routing this work item does not authorize analysis. A later
assignment begins it.

Do not promote findings, change current owners, archive this work item, or
create work item `036` without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, promotion, deferral, and closure.
- [Maintainer notes on compiler directives](../raw/maintainer-notes/compiler-directives.md) -
  the primary input and the maintainer's current direction.
- [Raw compile-time execution](../raw/compile-time-execution.md) - preserved
  compile-time questions, host and target context, constant availability, and
  deferred partial pressure.
- Legacy [`compiler-directives.md`](../../compiler-directives.md), its opening
  "Compiler Directives" section and "Official and extended directives" - the
  legacy framing of what directives are.
- [Zax source structure](../../language/source-structure.md#compiler-directive-enclosure),
  compiler-directive enclosure, and the
  [compiler-directive enclosure](../../language/terms.md#compiler-directive-enclosure)
  term - the current directive syntax boundary.
- [Zax namespaces and modules](../../language/namespaces-and-modules.md#nothing-policy-defaults),
  Nothing-policy defaults - a current directive whose scope-bound attachment
  any general model must preserve.

### Consequence-driven

Read only the smallest relevant sections when the concern reaches them:

- The remaining sections of the legacy
  [`compiler-directives.md`](../../compiler-directives.md), one directive at a
  time as each is dispositioned.
- Legacy [`meta-functions.md`](../../meta-functions.md) and
  [`meta-types.md`](../../meta-types.md) when compile-time functions or type
  computation are reached.
- Raw inputs on [export and visibility directives](../raw/export-and-visibility-directives.md),
  [analysis controls](../raw/analysis-controls.md),
  [the CPU provider model](../raw/cpu-provider-model.md), and
  [global and once lifetimes](../raw/global-and-once-lifetimes.md) when their
  concerns are reached.
- [Zax safety and analysis](../../language/safety-and-analysis.md#language-contracts-and-compiler-analysis)
  for language-contract selection.
- [Zax integer literals](../../language/integer-literals.md#concrete-results-and-compile-time-execution)
  and [Zax literal source](../../language/literal-source-and-operators.md#required-compile-time-execution)
  for existing required compile-time behavior.
- [Zax partials](../../language/partials.md) for `size of` before storage closes
  and hook-attachment detection.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md)
  only where the compile-time boundary itself is at issue.

### Audit-only

- Archived numbered work, only when a provenance question cannot be answered
  from current owners or raw input.

## Working record

### How to read this record

This record holds the results of review discussions from 2026-10-30 to
2026-11-01. It replaced the initial reconstruction of 2026-10-29, whose useful
content was folded in.

- **Part 1** is the catalog of everything this item keeps: purpose, examples,
  how it is written or attached, and anything still open.
- **Part 2** is a checklist of everything removed or deferred, with the reason
  and destination for each.
- **Part 3** lists consequences for current owners that a promotion must
  integrate. None of them has been edited.
- **Part 4** records the maintainer's instruction about how promotion must
  teach.

Each entry carries a maturity label:

| Label | Meaning |
| --- | --- |
| **Aligned** | The maintainer agreed in discussion. Still non-authoritative until promoted. |
| **Discussed** | Raised and not objected to, but not explicitly confirmed. |
| **Candidate** | Proposed but not yet discussed. |
| **Open** | Still needs a decision. |

Directive names, option names, and keywords are working spellings unless the
entry says the name itself is aligned.

---

## Part 1: Catalog of kept items

### 1. Compile-time code

#### 1.1 Two worlds

**Aligned.** A Zax program is effectively built twice from the same source:

- the **target program**, which is the program that ships and runs; and
- the **host world**, which is the same code compiled for the build machine and
  run during compilation.

The two are separate. Each has its own globals, its own `___` execution
context, and its own memory. The host world affects the target program only by
producing **plain values**, which become constants, and later **generated
code**. Generators are deferred (Part 2).

The purpose is pre-computing values and generating code. Compile-time code is
ordinary Zax, not a macro language, which preserves the vision goal of
compile-time abstraction without textual preprocessing.

#### 1.2 Terms, and which definitions each world uses

**Aligned.** Three separate vocabularies:

| Term | Meaning |
| --- | --- |
| **Execution context** | Only the runtime `___` object (type `ExecutionContext`). Unchanged. |
| **Environment** | Either the **compiler host** or the **target**. The **active environment** is the one whose definitions apply right now. `native` means the active environment. |
| **Compile time** and **runtime** | Timing only. |

"Context" is no longer used for host versus target.

**Aligned: each world uses its own definitions.** Inside the host world,
environment-dependent types have the host's definitions. `Integer` may be
64-bit on the host and 32-bit on the target, and byte order may differ too.

```zax
hostBytes := compile size of Integer          // the host's Integer size
targetBytes := compile target size of Integer // the target's Integer size
```

Compile-time code that computes target facts asks for them explicitly. This
reverses the current literal owner's rule that compile-time evaluation uses
target facts by default (Part 3).

**Aligned on hardware:** compile-time code may use host hardware, but hardware
must never influence a result. Hardware and software evaluation must produce
the same value.

#### 1.3 Choosing the world at the call site: `compile`, `compile{...}`, `runtime`

**Aligned.**

```zax
total += compile scale(4)  // the right-hand side becomes a constant; total stays runtime

compile{
  ++a
  logAToDisk(a)            // runs on the build machine during compilation
}

record(3)                  // a folding call: runs at compile time
runtime record(3)          // forced into the target program
```

**When `compile` points run:**

- A `compile` point runs when the compiler compiles its location, exactly once,
  whether or not runtime ever reaches it. One inside a loop runs once, not once
  per pass.
- The host world never runs `main` to collect `compile` points.
- Names inside a `compile` point refer to the host world.
- Runtime values are out of reach:

  ```zax
  input := readLine()
  size := compile measure(input)   // error: input is a runtime value
  ```

**Greedy reach.** `compile` and `runtime` each cover the rest of the complete
expression they start. Reach stops at an enclosing closing parenthesis, a
comma, or the end of the statement. Parentheses narrow it.

```zax
foo := compile bar() + 5 * (fooish() ^ 333) / 5   // the whole right-hand side
foo := (compile bar()) + 5 * (fooish() ^ 333) / 5 // only bar()
log(compile tableSize(), count)                   // stops at the comma
total := compile scale(4) + userInput             // error: userInput is a runtime value
```

Greedy reach makes a runtime value caught inside a loud error rather than a
silent split.

**Nesting:**

```zax
result := compile foo() * compile bar() * 5 + 3   // inner compile is redundant: lint, not error
runtime log(compile tableSize())                  // legal: pre-computes only the size
compile check(runtime readLine())                 // error: the host world cannot reach runtime
runtime buildTable(256)                           // error if buildTable is [<execute=compile>]
```

**`compile{...}` yields no value.** It is an effective statement, and its
effects happen in the host world. Results reach the target later through
persistent host globals:

```zax
compile{
  buildLookup()                   // fills a host-world global
}

entry := compile lookupEntry(3)   // a later point reads that state
```

**`runtime` is a keyword only where an expression follows it**, so a variable
named `runtime` remains possible.

#### 1.4 Crossing from host to target

**Aligned.** Only plain values cross. Pointers, references, host allocations,
and capturing lambdas never cross. A crossing value **keeps its type
identity**. When its definition differs between the worlds, the value is
converted without silent loss.

```zax
value : Scalars.Integers.Integer = compile makeMuchBigger(5)
// Same identity in both worlds; the host definition may be 64-bit and the
// target definition 32-bit, so the value is converted, with a range check.
```

A programmer who uses an identity whose definition is the same in both worlds
controls this, and then no conversion happens:

```zax
// Environment-fixed identity (Part 1, section 4)
crc : Scalars.Integers.Target.U32 = compile checksum(table) // identical definition: no conversion
```

Conversion rules:

| Value | Rule |
| --- | --- |
| Integers | Recommitted to the target definition with a range check. Out of range is a compiler error. |
| Reals, magnitude | Beyond the target format's finite range is a compiler error. |
| Reals, precision | Rounded correctly to nearest. This is inherent to narrowing a real, and deterministic. |
| Special values | NaN payloads, signalling NaN, and negative zero follow the target format's rules. A payload the target cannot represent is an error. |
| Byte order | Converted silently; it is representation, not value. |
| Composite values | Converted member by member. |
| Values of a type with a pending target layout | Cannot cross until the layout is sealed (1.5). |

**Aligned: no warning by default when a real rounds on crossing.** Nearly
every non-trivial real rounds when narrowed, so a warning would be noise. An
optional lint may report it. The consequence to teach: a value computed on the
host at higher precision and rounded once can differ from the same computation
done at runtime in the target's format.

```zax
area : Float = compile computeArea(radius) // host Float precision, then rounded to the target Float
area2 : Float = computeArea(radius)        // target Float throughout; may differ in the last bits
```

A programmer who needs bit-identical results computes inside compile-time code
with an environment-fixed identity (Part 1, section 4).

#### 1.5 How the host world is set up

**Aligned:**

- **One host world per module instance.** Host globals persist from one
  compile-time evaluation to the next in source order. A call into an imported
  module uses that module instance's own host world.
- **Globals start on first use, in dependency order.** A cycle is a
  diagnostic.

  ```zax
  tableSize : Integer = 256
  table : Integer[] = buildTable(tableSize)

  checksum := compile sum(table)
  // host world: initializes tableSize, then table, then runs sum
  ```

- **Initialization order differs between worlds, and that is accepted.** Target
  globals start in source order; host globals start on first use.
- **No waiting for program assembly.** Libraries finish compiling before
  assembly, because `compile` results decide types, sizes, and which
  declarations exist.
- **`___` is sealed at each `compile` point.** It includes the core members,
  imported partials, and this module's partials completed before the point.
  Between points the shape only grows: existing members keep their values, and
  new members start at their defaults. Granting a partial declared after the
  point is an error.

  ```zax
  first := compile countRequests()   // host ___ sealed with the partials so far

  MyTracking :: partial ExecutionContext {
    requestId : U64 = 0
  }

  second := compile nextRequest()    // re-sealed: requestId added, starting at 0
  ```

- **Pending target layouts wait.** A value whose type has a pending target
  layout cannot cross until the layout is sealed. A question about the
  target's sealed shape, such as `target size of ExecutionContext`, waits or
  is rejected the same way.
- **Writes to host globals are allowed** from every kind of compile-time
  execution, including literal bodies and `folding` calls:

  ```zax
  seed : U64 = 0x9E3779B9

  MyRandom :: type {
    operator literal 'rand' final : (result : U64)(
      payload : String immutable readonly final &
    ) unbound = {
      seed = mix(seed, payload)   // host-world write from a literal body
      return seed
    }
  }

  a := MyRandom.rand'x'
  b := MyRandom.rand'x'           // differs from a: a defined, source-ordered result
  ```

  This revises the current literal owner (Part 3).
- **Parallelism is the compiler's business.** A compiler may evaluate in
  parallel and fall back to sequential evaluation where host state is touched.
  Costs worth teaching: changing an early state-touching evaluation forces
  later ones to be recomputed, and moving code between files reorders
  evaluations.

**Aligned: reflection uses one identity with an environment-specific shape.**
`ExecutionContext` is the same declaration in both worlds. Plain reflection in
compile-time code describes the host world's shape at that point. A question
about the target's shape is explicit, and waits for the sealed shape or is
rejected before assembly, under the pending-layout rule:

```zax
compile{
  for member in members of ExecutionContext {          // illustrative: the host shape at this point
    emitAccessor(member)
  }
}

count := compile size of members of target ExecutionContext // illustrative: waits for the sealed target shape
```

This is the same principle as crossing (1.4): one identity, a definition per
environment.

#### 1.6 Callee execution policies: `[<execute=...>]`

**Aligned.** The policy sits in the declaration slot after `final`.

| Policy | Meaning | Does the result count as a constant? |
| --- | --- | --- |
| `[<execute=auto>]` (default) | Runtime meaning. The compiler may pre-compute work that has no observable effect. | No |
| `[<execute=runtime>]` | Never pre-computed: hardware access, target-only partials, or keeping a large table out of the binary. | No |
| `[<execute=folding>]` | Compile time whenever every argument, including a receiver, is constant. | Yes |
| `[<execute=compile>]` | Always compile time; an error if impossible. Never exists in the target program. | Yes |

An **observable effect** is anything visible outside the computation, such as
output, I/O, the clock, or state that outlives the call.

```zax
hash final [<execute=folding>] : (result : U64)(text : String readonly &) = {
}

buildTable final [<execute=compile>] : (result : Integer[])(size : Integer) = {
}

readPort final [<execute=runtime>] : (result : Byte)(port : U16) = {
}

main final : ()() = {
  key := hash("player")         // compile time: folding with a constant argument
  other := hash(readLine())     // runtime
  table := buildTable(256)      // compile time: the callee requires it
  answer := sumTo(1000)         // auto: may be pre-computed invisibly
  print(answer)                 // observable effect: stays at runtime
  value := compile readPort(1)  // error: readPort is runtime-only
}
```

Rules:

- **`auto` stays invisible.** Its results never count as constants for `is
  constant`, required-constant contexts, or array extents. An `auto` panic stays
  a runtime panic even if pre-computed. A warning is acceptable; a compile error
  is not.
- **`folding` and `compile` give real constants**, and their compile-time panics
  are compiler diagnostics.
- **Why `folding` is not the default.** A function with no parameters always
  has "every argument constant", so `main()`, `now()`, and `readConfig()` would
  run at compile time.
- **`folding` may write globals.** A ban would also disqualify locks, caches,
  and memoization. The consequence to teach is action at a distance:

  ```zax
  callCount : Integer = 0

  record final [<execute=folding>] : ()(value : Integer) = {
    ++callCount
  }

  record(3)            // folds: increments the host world's callCount
  record(limit)        // folds only while limit is a constant; making limit
                       // configurable silently moves this write to runtime
  runtime record(3)    // explicit: the target's callCount
  ```

  An optional lint may flag `folding` functions that write globals.
- **Call chains are checked**, and the diagnostic names the chain:

  ```text
  error: cannot run at compile time
    makeTable() calls checksum() calls readPort(), which is runtime-only
  ```

- **No language budget.** Compile-time evaluation is silent until it passes a
  threshold, then reports progress naming the function. Stopping is the build
  system's decision. An interrupted build shows the compile-time call stack.

**Function values and lambdas (aligned):**

1. The policy is part of the callable's type; a slot's policy must match the
   function installed in it.
2. A `compile`-only function has no runtime function value. Taking one is legal
   only inside compile-time code.
3. A call through a function value known at compile time follows the policy. A
   call through a runtime slot is a runtime call.
4. Lambdas use the same directive slot and default to `auto`. A lambda created
   in the host world crosses only if it captures nothing.

```zax
step final [<execute=folding>] : (result : Integer)(value : Integer) = {
  return value + 1
}

fixed := step         // a constant function value
one := fixed(0)       // folding still applies

slot : (result : Integer)(value : Integer) [<execute=folding>] = step // illustrative spelling
two := slot(0)        // runtime slot: a runtime call
```

**Aligned: replacement must be compatible, not identical.** The installed
function must be able to run in every world the slot can be called from:

| Slot policy | Can be called | Installable functions |
| --- | --- | --- |
| `auto` | at runtime, with invisible pre-computation | `auto`, `runtime`, `folding` |
| `runtime` | at runtime | `auto`, `runtime`, `folding` |
| `folding` | at compile time with constant arguments, otherwise at runtime | `folding` |
| `compile` | only at compile time | `compile`, `folding`, `auto` |

```zax
handler : (result : Integer)(value : Integer) = step   // auto slot: a folding function is fine
handler = readSensorValue                               // fine: runtime-only works in an auto slot

builder : (result : Table)(size : Integer) [<execute=compile>] = buildTable // illustrative spelling
builder = readTableFromPort                             // error: a runtime-only function can't run at compile time
```

A `compile`-only function never fits an `auto` or `runtime` slot, because
nothing exists for it to run at runtime.

#### 1.7 Compile-time selection: `if compile` and `switch compile`

**Aligned.**

```zax
if compile target size of Integer > 4 {
  useWideTables()
} else {
  useNarrowTables()
}

switch compile target endianness {
  case Endian.little
    useLittleCodec()
  case Endian.big
    useBigCodec()
}
```

- The condition or switch value must be a compile-time value.
- **Unselected branches are discarded.** They must only tokenize correctly with
  balanced delimiters. Nothing in them is parsed, resolved, or layout-checked,
  so they may name things that do not exist. A brace inside a string or comment
  must not mislead the delimiter count.
- An ordinary `if` with a constant condition is not a compile-time selection:
  both branches must still compile.
- `switch compile` case tests are compile-time values. Fallthrough keeps the
  selected case and the cases it falls into.
- **`if compile` is allowed where an ordinary `if` is not**, such as type and
  namespace bodies.
- **Branches always form scopes, everywhere** (option A). The deciding reason is
  that module top level is both a declaration area and executable code, so a
  context-dependent rule would be ambiguous there. Escaping names use `outward`
  (1.8).

Consequences to record:

- A typo in a rarely selected branch is found only when some target selects it.
  An optional "check all branches" lint may help.
- Formatters treat discarded branches as opaque text.

#### 1.8 `outward` declarations (replaces `descope`)

**Aligned.** `outward` is a declaration-side keyword, in the same position as
`shadowable`. It is placed just before `::` for `::` declarations, and its order
relative to other declaration words is still open. It works on any
declaration: variables, types, aliases, functions, and imports.

```zax
if useLarge {
  scratch := measure()                 // stays in the branch
  limit outward final : Integer = 1000
} else {
  limit outward final : Integer = 10
}

process(limit)
```

The deciding rule is that **every path reaching the code after the construct
provides exactly one resolution.**

- **Compile-time selection.** Branches may declare different things. Code using
  a selected declaration must itself be selected, or go through a stable alias.
  A missing resolution is an error where the name is used.

  ```zax
  if compile selectStructVersion(4) {
    Layout :: type {                       // helper: stays in the branch
    }
    MyTypeA outward :: type {
      data : Layout
    }
    MyRecord outward :: alias type MyTypeA
  } else {
    MyTypeB outward :: type {
    }
    MyRecord outward :: alias type MyTypeB
  }

  record : MyRecord                        // valid whichever branch was selected
  ```

- **Runtime selection** (`if`/`else if`/`else`, `switch`). Every path reaching
  the following code declares the same name with the same type and qualifiers.
  The result is one declaration, initialized along whichever path ran. A
  mismatch is an error at the construct. A path that leaves through `return`, a
  `break` to an outer target, or a panic needs no declaration.

  ```zax
  switch mode {
    case 1
      limit outward final : Integer = 10
    case 2
      limit outward final : Integer = 1000
    case 3
      return                               // leaves: needs no limit
    default
      limit outward final : Integer = 100
  }

  process(limit)
  ```

- **Fallthrough counts as one path.** Two `outward` declarations of one name
  along a fallthrough chain are a duplicate error.
- **Loops are excluded.** A repeating body would declare the name again on every
  pass, and a loop may run zero times.

  ```zax
  forever {
    if ready {
      item outward := take()   // error: escaping would land in a loop body
    } else {
      item outward := wait()
    }
  }
  ```

- **`shadowable` interaction.** An outward declaration behaves exactly as if it
  were declared in the scope it lands in. If that scope already declares the
  name, it is a duplicate. Outer scopes follow the normal `shadowable` rules.
- The declaration's lifetime ends with the scope it lands in.

**Aligned: how far it escapes.**

- An `outward` declaration lands in the scope that contains the **nearest
  enclosing named construct**: `if`, `switch`, or `scope`.
- Trivial `{ ... }` blocks are transparent; the declaration passes through them.
- An `if`/`else if`/`else` chain is one construct, so a declaration in any
  branch lands outside the whole chain.
- Nested constructs escape one level each:

  ```zax
  if outerCondition {
    if innerCondition {
      limit outward := 10   // lands in the outer if's branch, not beyond it
    } else {
      limit outward := 20
    }
    use(limit)              // fine here
  }
  use(limit)                // error: limit did not escape the outer if
  ```

  To escape further, restructure into one chain.
- If the nearest named construct is a loop, the declaration is an error.
- If there is no enclosing named construct, the declaration is an error. An
  example is `outward` in a plain block directly in a function body.

#### 1.9 `wellformed{...}`

**Aligned.** `wellformed{...}` is an expression that asks whether the enclosed
source forms valid code, and yields a compile-time `Boolean`. The payload is
checked as if it were the body of an immediately invoked lambda that captures
the surrounding scope. It is never executed, its declarations never escape, and
captured names have types but no values.

```zax
value := readSensor()

if wellformed{ ++value; --value; value *= 3 } {
  scaleUp(value)     // checked, never run: value is unchanged
}

if !wellformed{ Module.UseComplexMath } {
  UseComplexMath outward :: import Module.DefaultComplexMath
}
```

- **Attached brace.** `wellformed{` with no space is the expression, and
  `wellformed` alone is an ordinary name. The word is rare as a variable name.
- **Where checking stops.** The payload fully checks any code it creates, and
  trusts code that already exists. An existing non-generic function is checked
  through its prototype only; its body is checked on its own anyway. A generic
  instantiation created by the payload has its body checked with the new type.
  Exact depth for generics goes to the generics work.

  ```zax
  increment final : ()(value : MyCounter writable &) = {
    value.cuont += 1    // typo: an error reported in increment itself
  }

  ok := wellformed{ increment(myCounter) } // true: the call matches the prototype
  ```

- **Warnings must be decided explicitly, per category.** If a language-defined
  warning occurs in the payload and no option addresses its category, the
  `wellformed` itself is a compile error. Options name one category each, in
  the form `allow-<category>` or `reject-<category>`, and combine in a comma
  list. Deprecation is the only category today:

  ```zax
  if wellformed{ fastPath(data) } { ... }                             // error if fastPath is deprecated
  if wellformed<allow-deprecated>{ fastPath(data) } { ... }           // deprecation counts as well formed
  if wellformed<reject-deprecated, explain>{ fastPath(data) } { ... } // counts as failure, with notes
  ```

  This keeps a dependency's deprecation from silently switching which branch
  callers take. Only language-defined warnings count; lints never affect
  `wellformed`. Future warning categories join the same schema.
- **Intent errors are errors.** A payload that needs an intent acknowledgement
  must contain it:

  ```zax
  ok := wellformed{ first <+> second }                                     // false if the join needs acknowledgement
  ok := wellformed{ intent<same-prefix-literal-join>{ first <+> second } } // checks the acknowledged form
  ```

- **Seeing why it failed.** `wellformed<explain>{...}` still yields its
  result, but emits the payload's diagnostics as compiler notes, which are
  neither warnings nor errors. A compiler build option can also explain every
  `wellformed` failure, so debugging needs no source edits.
- **No effects.** Checking a payload changes no host state, declarations, or
  later results. A `compile` point inside a payload is type-checked, not run.
  That line numbers advance is a fact of where code sits, not an effect of
  checking.
- **No error-on-failure form.** To assert that something compiles, use
  existing features:

  ```zax
  compile{
    assert(wellformed{ ++sample })   // a compile-time panic becomes a compiler diagnostic
  }
  ```

#### 1.10 What counts as a compile-time value

**Aligned.** `if compile`, `switch compile`, "every argument constant" for
`folding`, and `is constant` all use this one definition. A compile-time value
is:

- a literal;
- an expression built only from compile-time values;
- the result of a `compile` point, an `[<execute=compile>]` call, or a `folding`
  call whose arguments are all compile-time values;
- a `Compilation` query, `size of`, or another compile-time query; or
- a **local `final` binding initialized from a compile-time value**.

A varying local is a runtime variable even when initialized from a constant,
because it can be reassigned. Inside compile-time code, a global is the host
world's copy (1.1). `is constant` answers by exactly these rules, never by what
the optimizer did.

```zax
limit final := 64
count := 64

main final : ()() = {
  size final := 16
  step := 4

  if compile size > 8 { }    // fine: size is a final local with a constant initializer
  if compile step > 2 { }    // error: step is a varying local, a runtime variable
  key := hash(limit)         // folds: limit is a compile-time value
  if compile count > 32 { }  // reads the host world's count
}
```

#### 1.11 Which `compile` points run

**Aligned.** Every `compile` point in a module's source runs, including those in
functions nothing references. A failing point in unused code still fails the
build, which keeps failures loud. Discarded branches of compile-time selection
are excluded. Generic bodies run once per instantiation; the details are
deferred to generics.

#### 1.12 Asking whether a hook has fulfillments

**Aligned rule; spelling deferred.** A compile-time question about whether a
partial fulfills a hook is answerable only where fulfillment is fixed: after the
type's module finalizes, or for an importer, after the import completes. Asked
earlier, it is an error, never a guess. Under `seal open storage module`, where
fulfillment is fixed only at program assembly, it is always an error at compile
time; the author uses a runtime check, or seals the type.

```zax
// Illustrative spelling only
+++ final : ()() = {
  if compile _.hooks.construct is fulfilled {
    prepareHookState()   // pre-setup cost exists only when a hook is fulfilled
  }
  _.hooks.construct()
}
```

The query counts included fulfillments, not visible ones, as the raw
compile-time input already states.

### 2. Directive basics

#### 2.1 Three families

**Aligned.**

| Family | What it does | Shape | Members |
| --- | --- | --- | --- |
| Directive | Describes one construct, opening, or point to the compiler | `[<name>]`, `[<name=value, option=value>]` | `inline`, `likely`, `deprecate`, `execute`, Nothing policy, layout, ... |
| Source enclosure | Changes how a visibly bounded source unit is analyzed or authorized; creates no scope and no value | `word<category>{ ... }`, `bare{ ... }` | `intent`, `unsafe`, `bare` |
| Compile-time expression or keyword | Evaluates or asks something and yields a value, or changes declaration meaning | Keyword or attached-brace form | `compile`, `runtime`, `compile{...}`, `wellformed{...}`, `outward`, `size of`, `is constant` |

Enclosure heads stay one plain keyword, and hyphenated names go in the
`<category>` slot.

#### 2.2 Forms and names

**Aligned:**

- **Grammar.** The first item names the directive, optionally with `=value`.
  Every later item is `key=value` or a bare flag. There are no positional path
  segments.

  ```zax
  [<file="inputs.csv">]
  [<file, generator="Acme FooBar Generator">]
  [<line=400, increment=0>]
  ```

- **`x-` prefix.** Unofficial directives and options use `x-`. Compilers are
  recommended to add a vendor prefix:

  ```zax
  run final [<x-acme-hot-path>] : ()() = {
  }
  ```

- **Vendor directives may refine meaning but never change it.** A change of
  meaning needs a new form, with the old form deprecated. A shared registry
  among cooperating vendors is recommended, not enforced.

**Aligned:** an unknown unprefixed name is an error, so `[<inlien>]` cannot
silently do nothing. An unknown `x-` name may be ignored, with an optional
warning.

#### 2.3 Three attachment kinds

**Aligned.**

```zax
[<never>]                          // unattached: alone on its line, blank lines around it

[<deprecate>]
oldFunc final : ()() = {           // attached: the directive line directly precedes the statement
}

if condition [<likely>] {          // slot: tags one part of a construct
  handleUsual()
} else {
  handleRare()
}
```

- **Unattached.** On its own line, alone or with other unattached directives,
  with no code on the line immediately before or after.
- **Attached.** On the line directly preceding one effective statement or
  declaration. This is preferred to placing it on the statement's line.
- **Slot.** At a defined position inside a construct: an `if` or `else if`
  branch, an `else`, a `case` label, or the declaration position after `final`.

Consequence: blank lines change meaning, so formatters must preserve blank
lines around directives.

#### 2.4 Ranges, openings, and restoring state

**Aligned.** Range directives (`deprecate`, panic controls, `export`) share one
rule set:

- Each **opening** starts with the state of the scope it sits in. An opening is
  a namespace, type, or function body, or similar.
- **A file acts as an opening for directive ranges.** A range at a file's top
  level ends at the end of that file. Every file starts from the module's state,
  so moving or reordering files never changes meaning.
- Directives inside an opening change the state from that point onward.
- The closing brace, or the end of the file, **restores** the enclosing state.
  This is the implicit push and pop, so explicit `push`/`pop` do not exist.
- An end marker chooses what to restore. `inherit` returns to the state the
  opening started with; `default` selects language defaults. This applies where
  the directive has a meaningful `inherit` (panic controls).
- **A file brought in by source inclusion starts with the includer's state at
  the inclusion line.** The programmer controls this by placing directives
  before or after the inclusion. The included file's own ranges still end at
  its end, restoring that state.

  ```zax
  [<source="safe.zax">]                         // included with the default panic state

  [<panic=never, fixed-arithmetic-overflow>]

  [<source="hot-loops.zax">]                    // included with the check disabled
  ```

- **`import` is a wall.** Directive state never crosses an `import`. An
  imported module starts from language defaults, or from its build-configured
  starting state (3.5), and then applies its own source.

#### 2.5 Kinds of effect

**Aligned.** Each directive states its kind:

| Kind | Effect | Examples |
| --- | --- | --- |
| Hint | No meaning change; the compiler may ignore it | `inline` levels, `likely`/`unlikely` |
| Unchecked promise | The programmer promises a fact. Where a check exists, it is on by default and controlled by a panic category, never by debug or release mode. Violation with the check disabled is undefined. | `always`/`never`, `lock-free`, `void`, disabled panic categories |
| Semantic control | Changes meaning | `execute`, `deprecate`, panic attachment, Nothing policy, layout |

A directive may carry an unchecked promise without an extra `unsafe` marker.

### 3. Kept directives

#### 3.1 `inline`

**Aligned.** It asks for inlining of a `final` function, in the slot after
`final`.

```zax
smallHelper final [<inline>] : ()() = {      // prefer inlining
}

hotPath final [<inline=always>] : ()() = {   // strongest request
}

coldPath final [<inline=never>] : ()() = {   // never inline
}
```

- The levels are `maybe` (the default without the directive), `likely` (a bare
  `[<inline>]`), `unlikely`, `always`, and `never`.
- `always` is a **strong hint**, never an error. For recursion, a compiler may,
  for example, set up a locally recursive call per use.

#### 3.2 `likely` and `unlikely`

**Aligned.** The tag marks the branch it applies to.

```zax
if parsed [<likely>] {
  useResult()
} else if retryable [<likely>] {
  retry()
} else {
  reportError()
}

if cacheHit {
  serve()
} else [<unlikely>] {
  rebuild()
}

switch value {
  case 1 [<likely>]
    handleCommon()
  case 2
    handleOther()
  default [<unlikely>]
    handleUnexpected()
}
```

- More than one likely branch is fine. Tagging every branch identically carries
  no information and gets a lint warning, not an error.
- In a `switch`, the tag applies to the `case` test, not its body.
- **Hints never change which case is selected or which test effects happen.**
  Tests run in order and the first match wins. A compiler may test a likely
  case first only when tests do not overlap and have no effects.

#### 3.3 `always`, `never`, and the unreachable marker

**Aligned.** These are unchecked promises with a default-on check.

```zax
if bufferReady [<always>] {    // promise: the condition is always true here
  flush()
}

switch mode {
  case 0 [<never>]             // promise: this case is never selected
    legacyPath()
  case 1 [<always>]            // promise: this case is always the one selected
    modernPath()
}

forever {
  value := random()
  if value >= 0 {
    continue
  }
  if value < 0 {
    continue
  }

  [<never>]                    // unattached: this point cannot be reached

}
```

- **Panic categories:**

  | Category | Fires when |
  | --- | --- |
  | `never-path-reached` | a `[<never>]` branch, case, or point is reached |
  | `always-path-missed` | an `[<always>]` branch or case is not the one taken |

  These replace the legacy `impossible-if-value`, `impossible-switch-value`,
  and `impossible-code-flow`, which split by construct for no useful control.
- **`always` on a case means it is selected**: the switch's selection lands on
  that case's test.
- **Relation to likely/unlikely.** A promise is something the compiler relies
  on; a hint is a probability. `always` with `likely` is redundant and gets a
  lint warning. `always` with `unlikely` is contradictory and is an error.

#### 3.4 `deprecate`

**Aligned.** It uses the attachment rules (2.3) and range rules (2.4).

```zax
[<deprecate>]                      // attached: exactly this declaration
oldFunc final : ()() = {
}

MyType :: type {

  [<deprecate=always>]             // unattached: range start

  a final : ()() = {
  }
  b final : ()() = {
  }

  [<deprecate=never>]              // unattached: range end

}                                  // a range also ends at the closing brace

[<deprecate=always>]
c final : ()() = {                 // intent error: range form placed as an attachment
}
```

- An attached directive on a type or namespace covers everything inside it.
- A standalone `[<deprecate>]` with nothing attached is an intent error.
- **Options:**
  - `context=import|all|local`;
  - `error`, which makes the warning an error; and
  - `min="x.y"` and `max="x.y"`, which refer to import versions.
- `min`/`max` are recorded as the current proposal, with a **review point in
  the future build and dependency work**, which owns import versioning.

#### 3.5 Panic controls: attachment and import inheritance

**Aligned** on attachment, using ranges only, with no enclosure form.
Categories and the registry stay in raw analysis controls. Disabling a
category promises that the condition cannot occur: the check is removed, and
violation is undefined.

```zax
[<panic=no, fixed-arithmetic-overflow>]          // attached: this statement only
total = total + delta

MyMath :: type {

  [<panic=never, fixed-arithmetic-overflow>]     // range inside an opening

  accumulate final : ()() = {
  }

  [<panic=inherit, fixed-arithmetic-overflow>]   // back to MyMath's starting state

  audit final : ()() = {
  }

}                                                // enclosing state restored
```

The category name is illustrative.

**Aligned: imports, inclusion, and build configuration.**

- **Panic state never crosses an `import`** (2.4). An importer cannot make
  undefined-behavior promises about code it did not write, and this matches how
  the Nothing policy is set by a module for itself. This supersedes the earlier
  maintainer idea that imports inherit the importer's state.
- **Source inclusion carries the state at the inclusion line** (2.4).
- **Build configuration sets a module's starting state**, before its own source
  runs, and source directives refine it from there. A build that disables a
  check for a dependency is an explicit, auditable choice by the build's owner.
- `default` still selects language defaults, so categories added as the
  language evolves are picked up.

**Aligned safeguard:** disabling requires naming categories, with no wildcard
"disable all", so a category added later is never silently disabled.

#### 3.6 `export`

**Aligned:** it uses the same attachment and range rules, including file-level
ranges. Exact export syntax remains with the raw export input.

```zax
// file: api.zax
[<export=always>]

open final : ()() = {
}
close final : ()() = {
}
// the range ends with the file

[<export>]
Helper :: type {               // attached: exported
}
```

**Aligned: what a range exports.** The raw export input requires that a range
never silently export an unrelated declaration. These rules satisfy it:

- A range is bounded by its opening or file (2.4).
- A range exports only declarations **written lexically inside it**, directly
  in that opening or file. It never exports injected declarations, declarations
  added by partials from elsewhere, or the contents of nested namespaces, which
  need their own range or attachment.
- A range never exports import bindings or aliases. Those need an attached
  `[<export>]`, preserving the baseline that importing never re-exports by
  itself.

```zax
[<export=always>]

open final : ()() = {               // exported
}

Math :: import Module.Math          // not exported by the range

[<export>]
Geometry :: import Module.Geometry  // exported: explicit attachment
```

#### 3.7 `nothing-instance-default` (current reference example)

This is current design, owned by `namespaces-and-modules.md` and
`nothing-instances.md`. It is the accepted example of a directive attached to
an opening:

```zax
[<nothing-instance-default=trap>]
namespace Services {
  First :: type {
  }
}
```

The attachment rules must stay consistent with it.

#### 3.8 Layout: `align`, `reserve`, `void`

**Aligned** on concepts. No current owner defines member-layout control:
`type-definitions.md` only requires that size, alignment, and padding be
discoverable. These directives need a home at promotion.

```zax
MyPacket :: type {
  size : Integer

  [<align=16>]
  id : Uuid

  [<reserve=100>]              // unattached: 100 unnamed bytes at this point

  [<void>]
  something : Uuid             // has an offset, occupies no bytes

  foo : U32                    // same offset as something
}
```

- **No reordering by default.** `reorder=none` is the default; `compact` and
  `speed` are opt-ins.
- **Environment-specific layout uses compile-time selection**, not a directive
  option:

  ```zax
  MyBlock :: type {
    if compile target size of Pointer > 4 {
      [<align=16>]
      data outward : Payload
    } else {
      data outward : Payload
    }
  }
  ```

- **`reserve` has no fill value.** Reserved bytes carry no value, like padding,
  and follow the unions owner's rules for padding and bits that carry no value.
- **Partials need no seal.** `reserve` and `void` are local placement, and a
  partial appending members does not change them. An author who needs a fixed
  total size, for a wire format for example, seals storage explicitly.
- **`void` carries its own promise.** Accessing `something` reads the bytes at
  its offset, including `foo` and possibly bytes beyond the type's storage. That
  is an unchecked reinterpretation, and the directive is the promise. No
  `unsafe` marker is added.
- Fix the legacy `void` example, which declares `MyPacket` but asserts on
  `MyType`.

#### 3.9 `lock-free`

**Aligned meaning; deferred (dry run 1, F5).** It removes the thread-safety
barrier around a `once` value's construction, and is an unchecked promise with
no extra marker (2.5). Current design has `once` functions only; `once` values
are still raw global and once lifetime input. So `lock-free` stays in the
reduced legacy page and the raw global and once input, and is promoted together
with `once` values.

```zax
giveMeMyType final : (myType : MyType &)() = {
  singleton once [<lock-free>] : MyType
  return singleton
}
```

The ordering and once-lifetime details belong to raw global and once lifetimes.

#### 3.10 Source-mapping overrides

**Aligned.** For generated Zax, these make diagnostics and reflection report
the original source. They are unattached and positional, which is acceptable
because they change only presentation, never program meaning.

```zax
[<file="inputs.csv">]
[<file, generator="Acme FooBar Generator">]
[<module, location="https://example.com/">]

[<line=21>]
apple : Integer
banana : Float

[<line=400, increment=0>]
donut : Float
```

**Read stays read, write stays write.** Directives exist only to *set*
source-mapping values. Reading system and environment values is done with
queries (3.11). Setting environment values from source is removed; it belongs
to build configuration.

#### 3.11 Compilation information: the `Compilation` namespace

**Aligned.** Compilation information is exposed through a protected
language-provided namespace, following the `Scalars` precedent. Its values are
compile-time constants by nature, so no `compile` keyword is needed.

**Aligned shape** (an initial version that may be revised later):

```zax
Compilation.here      : SourceLocation   // where the query appears
Compilation.caller    : SourceLocation   // default arguments only
Compilation.compiler  : CompilerInfo
Compilation.compilerHost : Environment
Compilation.target    : Environment
Compilation.build     : BuildInfo
Compilation.module    : ModuleInfo
Compilation.file      : FileInfo
```

| Type | Members |
| --- | --- |
| `SourceLocation` | `file : FileInfo`, `line : Integer`, `column : Integer`, `function : String`, `module : String` |
| `CompilerInfo` | `name : String`, `vendor : String`, `version : Version` |
| `Version` | `major`, `minor`, `patch : Integer`; `preRelease`, `build`, `text : String` |
| `Environment` | `name : String` (for example `"x86_64-linux"`), `architecture : String`, `os : String`, `endianness : Endian`, `hasFeature(name) : Boolean`, `setting(name)` (optional `String`: a build-configuration value) |
| `BuildInfo` | `configuration : String` (the profile name), `time : BuildTime`, `variable(name)` (optional `String`: the build machine's OS environment variable) |
| `BuildTime` | `unix : I64`, `nt : I64`, `text : String` (ISO 8601) |
| `ModuleInfo` | `name : String`, `location : String`, `path : String` (absolute on the host), `importVersion : Version` (optional), `hash : String`, `git : GitInfo` (optional) |
| `GitInfo` | `tag`, `branch`, `commit : String` |
| `FileInfo` | `path : String` (relative to the module root), `name : String`, `hash : String`, `generator : String` (optional), `modified : BuildTime` |

"Optional" means the value may be absent; promotion uses the current
optional-values spelling.

```zax
Compilation.caller.line                   // individual parts are reachable
Compilation.target.hasFeature("sse")
Compilation.target.setting("PROFILE")     // build configuration for the target build
Compilation.build.variable("PATH")        // the build machine's OS environment variable
Compilation.compiler.version.major
```

- **Everything in `Compilation` is a compile-time value.** Nothing in it becomes
  a runtime value. The target machine's environment variables exist only at
  runtime, so they come from an ordinary runtime library call (illustrative:
  `System.environment("HOME")`), not from `Compilation`. `setting` is build
  configuration, known at compile time for either environment.
- **OS environment variables live under `build`**, not `Environment`, because
  `compilerHost` and `target` share the `Environment` type and only the build machine
  has OS variables at compile time. This supersedes the earlier
  `Compilation.host.environment("PATH")`. `compilerHost` matches the accepted
  `CompilerHost` paths (dry run 1, F1).
- **Shared types.** `compilerHost` and `target` share `Environment`, every version uses
  `Version`, and `here` and `caller` share `SourceLocation`.
- **Every item is allowed.** These members are marked non-reproducible:
  `build.time`, `build.variable`, `module.path`, `module.git`, and
  `file.modified`. Build configuration can pin their values for reproducible
  builds.
- **Caller location.** `Compilation.caller` is valid **only in a default
  argument**, where it reports the call site. Everywhere else,
  `Compilation.here` reports where the query appears in the source.

  ```zax
  trace final : ()(where : SourceLocation = Compilation.caller) = {
    print(where.file.path, where.line)
  }

  trace() // reports this line
  ```

  This is a small, deliberate piece of compiler magic, and replaces the legacy
  rules about inlined contexts.

Exact member spellings may be revised when the owner is drafted.

### 4. Captured future ideas

- **`pure`** (aligned capture; becomes a raw input at promotion). It would be a
  declaration keyword promising no observable effects, checked by the compiler.
  Beyond folding, it would allow memoization and removing duplicate calls.
  Monads (Haskell tracks effects in types) are a full effect system; `pure` is
  the binary form. An effect system in the monad style is recorded as a
  possible extension, not the intent.
- **Environment-fixed identities: the current accepted spelling stays**
  (aligned, dry run 1, F1). `integers.md`, `endianness.md`, and `terms.md`
  already define them, family first:

  ```zax
  Scalars.Integers.U32                // the active environment's definition
  Scalars.Integers.Target.U32         // always the target's definition, in both worlds
  Scalars.Integers.CompilerHost.U32   // always the compiler host's definition
  Target.Integer                      // short routes
  CompilerHost.Integer
  Scalars.Integers.Big.U32            // absolute endian: already the same in both worlds
  ```

  Environment and absolute endian select a specialization within a family, so
  they share one slot. `CompilerHost` is kept over `Host`, because "host" also
  means the CPU side of a running GPU program, or the machine an embedded or
  virtualized program runs on. No sweep of the scalar owners is needed. The
  earlier top-segment proposal is withdrawn.

---

## Part 2: Removed and deferred checklist

### Removed

| Legacy item | Reason | Replacement or remaining need |
| --- | --- | --- |
| `descope` (directive and `inline=descope`) | Fights the scope braces create; the inline form breaks in many situations | `outward` (1.8) |
| `[<descope>] scope` on ordinary blocks | Mainly produced flow errors | None |
| `compilation` | Its only use was host versus target `size of` | Explicit `target`/`host` queries (1.2) |
| `compiles` as a directive | Is an expression; the name was too close to `compile` | `wellformed{...}` (1.9) |
| `resolve` (`trial`, `lazy`, `last`, `now`, `retry`) | Exposes compiler scheduling, while `forward` handles declaration order | Unreferenced code: every `compile` point runs (1.11). "Must resolve here" is covered by `compile{ assert(...) }` |
| `execute=dual`, `host`, `target` names | Mixed environment with timing | `auto`, `runtime`, `folding`, `compile` (1.6) |
| `tab-stop` | Physical tabs are already intent errors | None |
| `variables`, `types`, `functions` qualifier defaults | Copied code would behave differently | An optional project lint requiring explicit qualifiers. Revise `qualifiers.md` (Part 3) |
| Runtime panic suppression table and pre-panic check | Lets execution continue past a failed check | Disabling removes the check (3.5) |
| Literal value directives (`[<file>]`, `[<line>]`, `[<compiler,...>]`, ...) in directive form | A directive is metadata, not a value | `Compilation` queries (3.11) |
| Writing environment values from source (`[<compiler,host,env,...=...>]`) | Read stays read, write stays write | Build configuration |
| `push`/`pop` for directive state | Openings and files already restore state | 2.4 |
| `unchecked<category>{...}` panic enclosure idea | One mechanism suffices | Panic ranges (3.5) |
| `reserve` `initialize=` | Reserved bytes carry no value | None |
| `align`/`reserve` `compilation=host\|target` options | Environment-specific layout uses compile-time selection | `if compile` with `outward` (3.8) |
| Legacy `impossible-*` panic names | Split by construct without useful control | `never-path-reached`, `always-path-missed` (3.3) |
| Error-on-failure checks (`compiles=error`, `requires=error`) | Existing features cover it | `compile{ assert(wellformed{...}) }` (1.9) |

### Deferred

| Item | Destination | What reopens it | Constraint on current work |
| --- | --- | --- | --- |
| Constant-required parameters (legacy `[<compile>]` on inputs), as **compile-time parameters** | Generics | Generics review | Their value lies in the body using the argument at compile time, which is specialization. Motivation to keep: arguments that must not come from runtime data, such as format strings and SQL templates. The maintainer's `readonly`/`immutable`/`final` analysis becomes essential there. |
| `concept` | Generics | Generics review | None |
| `requires`, and `true`/`false` candidate enablement | Generics or callable selection | Generics review | A compile-time block captures only types and compile-time values |
| SFINAE-style selection | Generics | Generics review | Must follow the `wellformed` rule: check created code fully, trust existing code |
| `wellformed` checking depth inside generic instantiations | Generics | Generics review | 1.9 rule |
| Generators (`execute=generate`/`delegate`) | Reduced legacy page, then future generator work | Generator review | Must fit the two-world model, and must respect "no textual preprocessing" |
| `lock-free` | Reduced legacy page and raw global and once lifetimes | Review of `once` values | Aligned meaning: an unchecked promise with no extra marker (3.9) |
| `source` | Raw build and dependencies | Build review | File timestamp checks (`generated=yes`) conflict with reproducible builds; an included file starts with the includer's directive state at the inclusion line (2.4) |
| `asset` | Raw build and dependencies | Build review | Writing files from compile-time code is a host-world effect |
| `deprecate` `min`/`max` review point | Raw build and dependencies | Build review | Current proposal in 3.4 |
| `synchronous`/`asynchronous` | Raw async | Async review | None |
| `abi` | Raw interop | Interop review | `never` (no-return) and `interrupt` are not calling conventions, and `never` collides with the path directive |
| Panic categories and registry contents | Raw analysis controls and registry | Analysis-control review | Two new names proposed (3.3) |
| Panic function (`___.panic`) | Raw safety | Safety review | Maintainer direction: it may only capture state; on return the program ends gracefully; an internal pre-panic capture always runs |
| Exact `export` syntax | Raw export and visibility | Export review | Must use 2.3 and 2.4 |
| `pure` and effect typing | New raw input at promotion | Purity or effect review | `folding` stays meaningful without `pure` |
| Environment-fixed identities, further design | Scalar owners | Explicit host/target type design | The current family-first spelling stays (Part 1, section 4); crossing rules in 1.4 |
| Panic build-configuration mechanics | Raw analysis controls, and build and dependencies | Analysis-control or build review | Build configuration sets a module's starting state, and source refines it (3.5) |
| Exact hook-fulfillment query spelling | Partials, then reflection | Partials or reflection review | Answerable only where fulfillment is fixed (1.12) |
| Complete reflection surface (`members of`, ...) | Raw reflection | Reflection review | One identity with an environment-specific shape (1.5) |

---

## Part 3: Consequences for current owners (not edited)

A promotion must handle these together:

- **`language/literal-source-and-operators.md`:**
  - "target-context source uses target type/profile facts; compiler-host facts
    require an explicit compiler-host context" is reversed. Literal bodies are
    ordinary host-world code with no exception (dry run 1, F2): each world uses
    its own definitions, results cross under 1.4, and target facts are
    requested explicitly (1.2).
  - The determinism guarantee is restated: results depend on payload,
    declaration, explicit inputs, the selected environments, including the
    compiler host's definitions for any environment-relative type the body
    uses, and versioned language data. Host-locale independence stays. Authors
    who need build-machine independence use exact or environment-fixed types.
  - The ban on mutable compile-time state making literal results order-dependent
    is replaced by defined source-order host state (1.5).
- **Raw compile-time execution:**
  - Its target-format real realization section assumes target facts by default,
    and must be reconciled with 1.2 and 1.4.
  - Its hardware wording stays: hardware must not influence results.
  - Most of the file is consumed by Part 1, section 1.
- **`language/source-structure.md` and `language/terms.md`:** the
  `[<likely>] if condition` examples tag the statement, not a branch. They move
  to the branch slot (3.2). The attachment kinds and the formatter blank-line
  rule belong with the enclosure rules.
- **`language/qualifiers.md`:** the statement that defaults "may be changed for
  an applicable source context" is revised.
- **Terminology:** uses of "active execution context" meaning host versus
  target change to "active environment". `execution-context.md` keeps its
  meaning.
- **`language/namespaces-and-modules.md`**, and the raw generative-import
  fallback: `compiles` becomes `wellformed{...}`, and fallback declarations use
  `outward` (1.7, 1.8).
- **`language/declarations-and-bindings.md`:**
  - "Compile-time execution remains directed and inferred" becomes the
    four-policy model with invisible `auto` (1.6).
  - `outward` joins `shadowable` as a declaration word.
- **`language/core-flow-control.md` and `language/switch.md`:**
  - hint and promise slots;
  - `if compile` and `switch compile`;
  - `outward` path rules; and
  - the statement that hints never change selection.
- **`language/partials.md`:** pending-layout waiting extends to values crossing
  from the host world, and to the per-point sealing of host `___` (1.5). Its
  open item "compile-time detection of whether a hook point has fulfillments"
  receives the 1.12 rule, with spelling still deferred.
- **`language/function-invocation.md`:** the policy is part of the prototype;
  `Compilation.caller.*` defaults are evaluated at the call site.
- **`language/unions.md`:** reserved bytes follow its padding rules; `void` is
  a related unchecked reinterpretation.
- **Raw `analysis-control-registry.md`:** add `never-path-reached` and
  `always-path-missed` as proposals; retire the legacy `impossible-*` names
  there.
- **Scalar owners:** no path sweep, because the accepted family-first spelling
  stays (dry run 1, F1). `integers.md` may gain one link from
  environment-relative names to the crossing rules.
- **Raw analysis controls:** build-level contracts become a module's starting
  panic state, refined by source directives. Panic state never crosses an
  `import` (3.5).
- **Legacy `compiler-directives.md`:** reduced to a routing note plus only the
  legacy items that remain deferred (dry run 1, F4; listed in dry run 2).

---

## Part 4: Promotion must re-author for teaching

**Maintainer instruction (2026-11-01).** This record is dense review material.
It is insufficient to copy into current documentation. Teachability is
essential at promotion, and promoted documentation must not assume that the
reader already knows how Zax works.

What promotion must do:

- **Re-author for a reader with no presumed Zax knowledge.** Introduce
  environments, the two worlds, and timing in everyday language, with source
  examples, before using their terms.
- **Lead each owner with a usable mental model and common examples.** Exhaustive
  rules, tables, diagnostics, and corner cases come after.
- **Teach the valid model positively.** Drop discussion chronology, rejected
  alternatives, and "replaces `descope`"-style history unless a counterexample
  prevents a likely mistake.
- **For every example, name the reader's question** at that point. State the
  situation before the snippet and the consequence after it.
- **Run the documentation guide's checks:** the cold-reader, vocabulary-before-use,
  and concrete-consequence checks, plus a cold-reader review by an agent that
  has not read this record.

## Dispositions and promotion dry run

### Dry run 1 (2026-11-01): FAIL

The dry run found five questions that need alignment before promotion can
begin. Everything else maps to a coherent owner structure. The structure, the
change set, and the teaching plans below assume this section's recommendations;
they change only where a decision goes another way.

#### Findings that need alignment

**F1. Environment-fixed identities already have an accepted spelling.**
Current owners already define environment-relative paths, in a different shape
from the aligned top-segment proposal (Part 1, section 4):

```zax
// Current accepted design (integers.md, endianness.md, terms.md)
myTarget : Scalars.Integers.Target.U32
myCompilerHost : Scalars.Integers.CompilerHost.U32
Target.Integer          // short route
CompilerHost.Integer
myWireValue : Scalars.Integers.Big.U32   // absolute endian, in the same slot
```

`terms.md` already says the target environment "may be named explicitly by
environment-relative type paths". `endianness.md` alone has 27 `Scalars.` uses.
The current form also places environment beside the absolute endian names
(`Big`, `Little`), which are already environment-fixed. Recommendation: **keep
the accepted current spelling**, which is also the maintainer's first
illustration, and withdraw the top-segment proposal. For the same reason,
`Compilation.host` may better be `Compilation.compilerHost`, to match
`CompilerHost` and the term "compiler host".

**F2. Literal bodies: host world, or target context?** The current literal
owner evaluates literal bodies in "the enclosing active execution context", so
a literal in target code uses target facts. The aligned model (1.2) says the
host world uses host definitions. These disagree for literals:

```zax
MyBig :: type {
  operator literal 'big' final : (result : MyBig)(
    payload : String immutable readonly final &
  ) unbound = {
    digits : Integer = parseDigits(payload) // host Integer or target Integer?
  }
}

value := MyBig.big'99999'   // under the host model, a 16-bit-Integer host
                            // and a 64-bit-Integer host can disagree
```

Two options:

- **(a) Literal bodies join the host world.** One model everywhere. Results can
  then depend on the build machine unless the literal uses exact or
  environment-fixed types.
- **(b) Literal bodies keep target-context evaluation.** This preserves the
  accepted guarantee that a literal's result depends only on its payload,
  declaration, explicit inputs, and the selected environment, never on which
  machine builds it. It makes literals a second, deliberate kind of
  compile-time evaluation.

Recommendation: **(b)**. A literal denotes a target value, and build-machine
independence for source literals is worth one explicit exception. The 1.5
decision that literal bodies may write host globals then needs restating as
"literal bodies may write the module's compile-time state".

The broader point is already accepted in `integers.md`: "Exact types or
explicit environment paths are used when representation must agree across that
boundary". Promotion must teach that `compile` results can depend on the build
machine whenever environment-relative types differ, and how to avoid it.

**F3. Where layout directives live.** No current owner defines member-layout
control. Recommendation: `type-definitions.md` gains an "explicit member layout"
section owning `align`, `reorder`, `reserve`, and `void`, because it already
owns stored members and requires that size, alignment, and padding be
discoverable. The directives owner lists them and links there. The alternative
is to keep their full meaning in the directives owner.

**F4. Generator material in the legacy page.** The maintainer asked to leave
generator material in the legacy page for now. But the documentation rules
retire a legacy page once its useful input has moved, and keeping it public
keeps every other legacy directive visible too. Two options:

- **(a)** Move the generator sections (`execute=generate`/`delegate`, with their
  examples) into a new raw input, `project/raw/source-generation.md`, and retire
  `compiler-directives.md`.
- **(b)** Reduce `compiler-directives.md` to its generator sections only, with a
  routing note.

Recommendation: **(a)**. It keeps the material intact without leaving a
partial legacy directive page in the public reading path.

**F5. `lock-free` has no current home.** Current design has `once`
*functions* only. `once` *values*, such as the local `singleton once :
MyType` in the `lock-free` example, are still raw global and once lifetime
input. A current owner cannot teach a directive on a construct that isn't
current. Recommendation: move `lock-free` to raw global and once lifetimes,
carrying its aligned meaning: it is an unchecked promise and needs no `unsafe`
marker. It is promoted when `once` values are.

#### Non-blocking notes

- **Capacity limits are not budgets.** `literal-source-and-operators.md` keeps
  "practical compiler capacity and work limits" for exact rational evaluation of
  numeric source. That concerns compiler arithmetic, not user compile-time
  code, so it does not conflict with "no language budget" (1.6). Promotion
  should state the difference.
- **Terminology is already mostly current.** `terms.md` already defines active
  execution environment, compiler host, and target environment. The sweep is
  small: `literal-source-and-operators.md` ("active execution context") and raw
  inputs.
- **`Compilation.caller` fits current invocation.** Defaults are already
  caller-side setup in `function-invocation.md`.
- **The partials boundary is ready.** Its open item on hook detection receives
  1.12 directly.

#### Ownership map

| Aligned finding | Lasting owner |
| --- | --- |
| Two worlds, environments and timing, `compile`/`compile{...}`/`runtime`, crossing, host-world setup, execute policies, compile-time selection, `wellformed`, compile-time values, which `compile` points run, `Compilation` | **New** `language/compile-time-code.md` |
| Directive families, grammar, names and `x-`, attachment kinds, ranges and openings, kinds of effect, `deprecate`, source mapping, directive catalog | **New** `language/compiler-directives.md` |
| `outward` | `language/declarations-and-bindings.md`, with path rules taught in flow and switch |
| `likely`/`unlikely`, `always`/`never` on branches, the unreachable marker, `outward` in `if` chains | `language/core-flow-control.md` |
| `case` hint and promise slots, `switch compile` selection details, `outward` with fallthrough, "hints never change selection" | `language/switch.md` |
| `inline`, policy in the prototype, replacement compatibility, `Compilation.caller` defaults | `language/function-invocation.md` |
| Policies on function values and lambdas | `language/lambdas-and-callable-composition.md` |
| Panic-control attachment, the import wall, build starting state, named categories | `language/safety-and-analysis.md` (categories stay in raw) |
| Layout directives (F3) | `language/type-definitions.md` |
| Reserved bytes as padding | `language/unions.md` (one cross-reference) |
| Host `___` per `compile` point | `language/execution-context.md` (short section) |
| Hook-fulfillment rule; pending layouts crossing | `language/partials.md` |
| Literal compile-time evaluation (F2) | `language/literal-source-and-operators.md` |
| Concrete results under the execute policies | `language/integer-literals.md` |
| Removal of qualifier-default directives | `language/qualifiers.md` |
| `Compilation` as a protected name; `wellformed`/`outward` fallback; the import wall | `language/namespaces-and-modules.md` |
| `is constant` meaning | `language/operator-catalog.md` (link to 1.10's owner) |
| Enclosure recognition for `compile{`, `wellformed{`; directive attachment and blank lines | `language/source-structure.md` |
| New and corrected terms | `language/terms.md` |
| `pure` and effect typing | **New** raw `project/raw/purity-and-effects.md` |
| Generators (F4) | **New** raw `project/raw/source-generation.md` |
| Deferred items in Part 2 | Their named raw inputs |

#### Every affected existing file

- **Current owners:**
  - `source-structure.md`, `terms.md`, `declarations-and-bindings.md`;
  - `core-flow-control.md`, `switch.md`;
  - `function-invocation.md`, `lambdas-and-callable-composition.md`;
  - `literal-source-and-operators.md`, `integer-literals.md`, `qualifiers.md`;
  - `namespaces-and-modules.md`, `partials.md`, `type-definitions.md`,
    `unions.md`;
  - `safety-and-analysis.md`, `operator-catalog.md`, `execution-context.md`.
- **Possible:** `integers.md`, `endianness.md`, `fixed-point-scalars.md`, and
  `floating-point-scalars.md`, only for a link from environment-relative names
  to crossing. With F1's recommendation, no sweep is needed.
- **Routers:** `index.md` (start-here entries for the two new owners, and the
  "Build-time programming and modules" entry), `project/raw/README.md`.
- **Raw inputs:**
  - `compile-time-execution.md` retires; its leftovers move (below).
  - `maintainer-notes/compiler-directives.md` retires.
  - `analysis-controls.md`, `analysis-control-registry.md` (including its link
    to the legacy panic registry anchor),
    `export-and-visibility-directives.md`;
  - `build-and-dependencies.md`, `global-and-once-lifetimes.md`, `safety.md`,
    `interop.md`, `async.md`;
  - `type-parameters-and-generics.md`, `reflection.md`, `cpu-provider-model.md`,
    `numeric-type-families.md`, `feature-catalog.md`,
    `pointer-and-arena-mechanics.md`.
- **Legacy pages:** `compiler-directives.md` retires (F4). The routing notes in
  `basics.md`, `meta-functions.md`, and `meta-types.md` point to the new owners.
- **Project:** this file only, until closure.

#### Raw compile-time execution: where each section goes

| Section | Destination |
| --- | --- |
| Current literal boundary | Literal owner (F2) and compile-time owner |
| Exact uncommitted real evaluation | Literal owner already holds the accepted part; capacity guarantees and caching move to raw numeric type families |
| Directed and inferred execution | Compile-time owner (1.6) |
| Native, compiler-host, and target context | Compile-time owner (1.2) |
| Context-sensitive layout and constant queries | Compile-time owner; "unusual widths and padding" moves to raw CPU provider model |
| `is constant` | Compile-time owner (1.10) |
| Type-qualified execution | Compile-time owner (no special rule); "type argument alone forces compile time" moves to raw generics |
| Concrete results and constant inputs | Integer-literal owner and compile-time owner |
| Target-format real realization | Scalar and literal owners (F2); crossing rules in the compile-time owner |
| Capability-dependent static selection | Compile-time owner (`if compile`, `wellformed`, `hasFeature`); support classification and size limits move to raw CPU provider model; bounded type probing moves to raw generics |
| Compile-time availability and failure | Compile-time owner |
| Partials, hooks, pending layouts | Partials owner and compile-time owner |
| Constraints and enum note | Already held by current owners; checked during promotion |

#### Structure proposal

- **Add two current owners:** `language/compile-time-code.md` and
  `language/compiler-directives.md`. The second is a new current owner, not
  the root legacy page.
- **Add two raw inputs:** `purity-and-effects.md` and `source-generation.md`,
  each indexed in `project/raw/README.md` with activation pressure and
  retirement.
- **Retire** the root `compiler-directives.md`, raw
  `compile-time-execution.md`, and raw `maintainer-notes/compiler-directives.md`.
  This 035 record keeps their provenance until it is archived.
- **No new directories.** `index.md` routes to both owners; raw material stays
  out of ordinary reading.

#### Proposed promotion batches

Each batch is reviewable on its own, and is validated before the next begins.

1. **Compile-time model.**
   - Create `compile-time-code.md`.
   - Change `terms.md`, `execution-context.md`, `literal-source-and-operators.md`,
     `integer-literals.md`, `operator-catalog.md`, `declarations-and-bindings.md`
     (the execution line and `outward`), and `partials.md`.
   - Retire raw `compile-time-execution.md` after moving its leftovers.
2. **Directives.**
   - Create `compiler-directives.md`.
   - Change `source-structure.md`, `terms.md`, `core-flow-control.md`,
     `switch.md`, `function-invocation.md`, `lambdas-and-callable-composition.md`,
     `type-definitions.md`, `unions.md`, `safety-and-analysis.md`,
     `namespaces-and-modules.md`, and `qualifiers.md`.
3. **Raw and legacy consumption.**
   - Move legacy material into its raw destinations, and create the two new raw
     inputs.
   - Update `analysis-controls.md`, the registry, and the other raw files listed
     above.
   - Retire the legacy page and the maintainer notes.
   - Update `index.md`, `project/raw/README.md`, and the legacy routing notes.

After batch 2, an agent that has not read this record reviews the changed
owner sections as a cold reader.

#### Teaching plans

| Owner section | Why the reader is there | What they know | What changes for them | What they might wrongly try |
| --- | --- | --- | --- | --- |
| `compile-time-code.md` opening | Wants to pre-compute a value | Ordinary Zax functions | The program is built twice; `compile` pre-computes one expression | Expecting `compile` to see runtime locals, or effects to land in the running program |
| Crossing | A `compile` result lands in a target variable | `Integer` has a size | One identity, a definition per environment; range errors, real rounding | Assuming host and target `Integer` are the same width |
| Execute policies | Writes a library function | Prototypes | Callee policies versus the caller's keyword | Making `folding` the default, or expecting `auto` results to count as constants |
| Compile-time selection and `outward` | Needs per-target declarations | Ordinary `if` scoping | `if compile` discards branches; `outward` lifts named declarations | Expecting a constant ordinary `if` to discard, or `outward` to escape loops |
| `wellformed` | Wants to probe for an API | Compile errors | A Boolean check of visible contracts; explicit warning choices | Expecting it to check callee bodies, or deprecation to pass silently |
| `Compilation` | Wants a version or source location | Constants | A protected compile-time namespace; `caller` only in defaults | Reading the target machine's environment variables |
| `compiler-directives.md` opening | Meets `[<...>]` in code | Statements and declarations | Directives describe a construct, opening, or point; three attachment kinds | Putting a directive on the statement's own line, or relying on blank-line-free placement |
| Ranges | Wants to deprecate or export many declarations | Openings | Ranges end at closing braces and files; `import` is a wall | Expecting a range to cross files or imports, or to export imports |
| Flow and switch slots | Optimizing branches | `if`/`switch` | Branch slots for hints and promises; `[<never>]` | Tagging the whole `if`, or confusing promises with hints |
| `inline` | Performance | Functions | A hint; `always` is a strong hint | Expecting an error when inlining is impossible |
| Panic controls | Disabling a check | Panic categories | Attachment and ranges; named categories only; build starting state | Expecting an importer's setting to reach its imports |
| Layout | Matching a wire or hardware layout | Stored members | `align`, `reserve`, `void`, `reorder`; `void` reinterprets storage | Expecting reordering by default, or `void` access to be checked |

**Result: FAIL.** Promotion waits for decisions on F1 to F5. Once they are
aligned, a follow-up dry run should be short.

### Dry run 2 (2026-11-01): PASS

Dry run 1's structure, ownership map, affected files, raw-section destinations,
batches, and teaching plans stand, except for the changes below.

#### Decisions (aligned 2026-11-01)

- **F1.** The accepted family-first spelling stays: `Scalars.Integers.Target.U32`,
  `Scalars.Integers.CompilerHost.U32`, `Target.Integer`, and
  `CompilerHost.Integer`. `CompilerHost` is kept over `Host`, and the
  compile-time namespace uses `Compilation.compilerHost`. There is no scalar
  sweep.
- **F2.** Literal bodies are ordinary host-world code. There is no exception.
  The literal owner's determinism guarantee is restated (Part 3).
- **F3.** Layout directives live in a new explicit-member-layout section of
  `type-definitions.md`.
- **F4.** The legacy page keeps only deferred legacy items. No
  `source-generation.md` raw input is created.
- **F5.** `lock-free` is deferred, with its aligned meaning, to the reduced
  legacy page and raw global and once lifetimes.

#### Changes to dry run 1's plan

**The reduced legacy page.** `compiler-directives.md` becomes a routing note,
naming the two new current owners, followed by only these sections, kept
verbatim except for enclosure normalization:

- generators (`execute=generate`/`delegate`, with their examples);
- `concept`;
- `requires`, with candidate enablement;
- the constant-required parameter (`compile` on inputs);
- `source` and `asset`;
- `synchronous`/`asynchronous`;
- `abi`;
- the panic function;
- `lock-free`.

Everything else leaves the page:

- the aligned, removed, or replaced material;
- the export directive, already preserved in raw export input; and
- the panic registry, already preserved in the raw registry.

Each raw destination links to the legacy section it will consume:

| Raw destination | Legacy sections it consumes |
| --- | --- |
| Build and dependencies | `source`, `asset` |
| Async | `synchronous`/`asynchronous` |
| Interop | `abi` |
| Safety | panic function |
| Global and once lifetimes | `lock-free` |
| Type parameters and generics | `concept`, `requires`, constant-required parameter |
| Legacy page itself | generators, until generator work |

`index.md` keeps the legacy entry, relabelled as deferred legacy directive
input, beside the new owners.

**Raw inputs.** Only `purity-and-effects.md` is created. The registry's link to
the legacy panic-registry anchor changes to its own preserved list.

**Ownership map.** The "Generators (F4)" row now points to the reduced legacy
page. The `lock-free` row points to the reduced legacy page and raw global and
once lifetimes.

**Batch 3.** Reduce the legacy page instead of retiring it.

**Teaching plan addition.** The crossing section must teach why a `compile`
result can depend on the build machine when an environment-relative type's
definition differs between worlds, and that exact or environment-fixed types
avoid it. This applies to literal bodies too.

#### Checks in this run

- **Nothing policy and the range rules.** `nothing-instances.md` attaches
  `[<nothing-instance-default=...>]` to one opening and allows one module-level
  default. It is deliberately not a "from here onward" setting. This is
  consistent. The directives owner must state that range rules apply only to
  range directives (`deprecate`, panic controls, `export`), and must give each
  directive's permitted attachment kinds. The Nothing policy is attached-only.
- **Literal owner.** Under F2, the literal owner's remaining guarantees still
  hold:
  - host-locale independence;
  - no compiler allocation leaking into results;
  - rejection and panic becoming diagnostics; and
  - no retry after selected compile-time unavailability.

  Only its environment wording and order-dependence ban change.
- **Environment terms.** `terms.md` "Active execution environment", "Compiler
  host", and "Target environment" already match 1.2. One wording fix remains in
  `literal-source-and-operators.md`.
- **No orphaned material.** Every Part 2 row has a live destination. Every raw
  compile-time section has a destination. Each legacy section either moves to a
  current owner, stays as deferred legacy input linked from its raw
  destination, or is preserved elsewhere.

**Result: PASS.** The aligned findings have a coherent owner structure, a human
reading path, deferred destinations, and an exact promotion set in three
batches. This result does not authorize promotion.

### Promotion record (2026-11-01)

Promotion was authorized and applied in the three planned batches.

- **New current owners:** `language/compile-time-code.md` and
  `language/compiler-directives.md`.
- **Current owners changed:**
  - `declarations-and-bindings.md` (outward declarations, execution line,
    access default);
  - `core-flow-control.md`, `switch.md`, `function-invocation.md`,
    `lambdas-and-callable-composition.md`;
  - `type-definitions.md` (explicit member layout), `unions.md`;
  - `safety-and-analysis.md` (controlling panic checks);
  - `namespaces-and-modules.md` (export directives, import wall, `Compilation`,
    fallback);
  - `qualifiers.md`, `literal-source-and-operators.md`, `integer-literals.md`,
    `integers.md`, `integer-operator-catalog.md`, `operator-catalog.md`;
  - `partials.md`, `execution-context.md`, `source-structure.md`, `terms.md`.
- **Routers:** `index.md` and `project/raw/README.md`.
- **Raw:**
  - `compile-time-execution.md` is retired. Its leftovers went to
    `cpu-provider-model.md`, `numeric-type-families.md`, and the existing
    generics probing section.
  - `purity-and-effects.md` is new.
  - Updated: `analysis-controls.md`, `analysis-control-registry.md` (legacy
    meanings preserved, two accepted categories), `export-and-visibility-directives.md`,
    `build-and-dependencies.md`, `global-and-once-lifetimes.md`, `safety.md`,
    `interop.md`, `async.md`, `type-parameters-and-generics.md`,
    `reflection.md`, `feature-catalog.md`, and `pointer-and-arena-mechanics.md`.
- **Legacy:** `compiler-directives.md` is reduced to its deferred sections,
  preserving the maintainer's unstaged `print final` correction. The routing
  notes in `basics.md`, `meta-functions.md`, and `meta-types.md` were updated.

Two deviations from the dry run:

- **Maintainer notes not yet deleted.** The maintainer's filled notes exist only
  as unstaged working-tree changes, so deleting
  `maintainer-notes/compiler-directives.md` now would destroy them with no
  version-history copy. The file is marked consumed in the raw index; deletion
  waits until the notes are committed.
- **Fixed-input links.** This file's fixed initiating input links the retired
  raw `compile-time-execution.md`. The input is immutable, so those two links
  become historical, which is the expected state once this record is archived.

Teaching choices made during re-authoring, beyond the dry run:

- Environment facts are asked for through existing environment-relative type
  routes (`size of Target.Integer`), not new `host`/`target` operator phrases.
- `switch` has no implicit fallthrough, so the `outward` and `switch compile`
  rules are stated in terms of `goto` body entry.
- Reorder keeps construction and destruction in declaration order.

#### Cold-reader review follow-up

An agent that had not read this record reviewed the changed owners. Fixes
applied:

- **`wellformed` fallback.** The fallback is now `if compile !wellformed{...}`.
  A plain `if` was not a compile-time selection.
- **Source mapping.** The examples now use unattached markers, with blank lines.
  `line` applies to the first following code line.
- **Range markers, made consistent.** `always`, `never`, `inherit`, and
  `default` are state markers that set the state from that point on; there is
  no separate "end" marker. The attached forms are bare
  `[<deprecate>]`/`[<export>]` and `panic=yes`/`no`.
- **Compile-time values.** `final` globals count, not only locals. Reading any
  global inside compile-time code gives the host copy.
- **Compile points.** `wellformed` payloads are the second "never runs"
  exception. `compile` stops at an `if compile` or `switch compile` body brace.
- **Slots and crossing.**
  - Policies on non-`final` slots are written after the name.
  - A call through a `compile` slot is always a compile-time value.
  - Arrays and strings cross as copies of their contents.
- **Examples repaired:**
  - the compile-time `___` example;
  - the environment-fixed type example;
  - the slot example; and
  - `myLimit` and `myRadius`, now declared.
- **Outward in loops.** A branch inside a loop lands in the loop body, which is
  valid. Only an `outward` whose nearest enclosing construct is the loop is an
  error. This corrects the earlier example, which contradicted the aligned
  nearest-construct rule.
- **Smaller fixes:**
  - `reorder` is added to the catalog;
  - `inline=never` is always honored;
  - an unattached attached-only directive is an error;
  - the `wellformed<options>{` opener is recognized;
  - the "Directive range" term is moved into alphabetical order; and
  - the import starting-state wording is aligned with build configuration.

**Decided (maintainer, 2026-11-01): hook-fulfillment timing.** The
query waits for fulfillment to be fixed, as `size of` waits for a pending
layout, and is an error only for a cycle or under `seal open storage module`.
`partials.md` now says so. The original concern follows. A type's own
constructor sits in the type's own module, so a compile point there runs before
that module finalizes. Under 1.12, asking whether its hook is fulfilled is
therefore always an error in the case that motivated the feature. The example's
spelling was changed from `_` to the type name, but the timing rule needs a
decision. One option is for such a query to wait for module finalization, the
way `size of` waits, and be an error only when waiting would create a cycle.
