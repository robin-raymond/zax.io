# Zax compiler directives

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading or writing `[<...>]` directives, and anyone deciding whether a control belongs in a directive at all |
| Applies To | What a compiler directive is, how one is written and named, where it attaches, directive ranges, the promises a directive may carry, `deprecate`, source mapping, and the catalog of current directives; not a formal grammar |
| Implementation State | Not established by this repository |
| Owns | The directive mental model; directive, enclosure, and compile-time-expression families; directive grammar; official and `x-` names; unattached, attached, and slot attachment; directive ranges and how openings, files, inclusion, and imports bound them; hint, unchecked-promise, and semantic-control kinds; `deprecate`; source-mapping directives; the directive catalog |
| Does Not Own | The `[< ... >]` token enclosure ([source structure](source-structure.md#compiler-directive-enclosure)); execution policies and compile-time code ([compile-time code](compile-time-code.md)); individual directives' meanings, which belong to the owners named in the [catalog](#directive-catalog) |
| Source / Provenance | Legacy compiler-directive input, reconciled with current source structure, Nothing policy, module, flow, safety, and compile-time design |

## Telling the compiler something about your code

Some facts about code are not part of what the code computes, but the compiler
still needs them: a declaration is deprecated, one branch is the common case, a
function should be inlined. A **compiler directive** states such a fact:

```zax
[<deprecate>]
drawLineOld final : ()(from : Point, to : Point) = {
}

render final : ()() = {
  if cacheHit [<likely>] {
    drawFromCache()
  } else {
    redrawEverything()
  }
}
```

A directive is written between `[<` and `>]`. It describes something to the
compiler: one construct, one region of declarations, or one point in the code.
It is not a value and not an expression. `[<likely>]` above does not compute
anything; it tells the compiler which branch to optimize for.

Some directives change what a program means, such as `deprecate` or the Nothing
policy. Others are only hints that a compiler may ignore. Each directive says
which it is.

### Directives, enclosures, and compile-time expressions

Zax has three different source forms that can look related. Choosing between
them follows what the form does:

| Form | What it does | Examples |
| --- | --- | --- |
| Directive, `[<name>]` | Describes a construct, a region, or a point to the compiler | `[<inline>]`, `[<deprecate>]`, `[<likely>]` |
| Source enclosure, `word<category>{ ... }` | Changes how a visibly bounded piece of source is checked or authorized. It creates no scope and no value. | `intent<...>{ }`, `unsafe<...>{ }`, `bare{ }` |
| Keyword or compile-time expression | Computes or asks something and yields a value, or changes what a declaration means | `compile`, `wellformed{ }`, `target size of`, `outward` |

So `wellformed{...}` is an expression, because it yields a `Boolean`, and
`outward` is a declaration keyword, because it changes where a name belongs.
Neither is a directive. Enclosure heads are single plain words; any hyphenated
name sits in the `<category>` slot, as in `intent<same-prefix-literal-join>`.

## Writing a directive

A directive starts with its name, which may carry a value. Later items are
named options or bare flags:

```zax
[<inline>]                                   // name only
[<inline=always>]                            // name with a value
[<deprecate, context=all, error>]            // name, a named option, and a flag
[<file, generator="Acme Report Generator">]  // name and a named option
```

Every item after the first is either `key=value` or a flag; meaning never
depends on an item's position in the list. Several directives may sit side by
side:

```zax
[<deprecate>][<inline>]
drawLineOld final : ()(from : Point, to : Point) = {
}
```

The `[<` and `>]` tokens themselves are described by
[source structure](source-structure.md#compiler-directive-enclosure).

## Where a directive goes

A directive's position decides what it applies to. There are three kinds of
position:

```zax
[<never>]                          // unattached: alone on its line

[<deprecate>]
drawLineOld final : ()() = {       // attached: the line directly below
}

if cacheHit [<likely>] {           // slot: inside a construct, at a defined place
  drawFromCache()
}
```

- **Unattached.** The directive is on its own line, alone or with other
  unattached directives, and the lines immediately before and after it hold no
  code. It applies to a point or begins a [range](#directive-ranges).
- **Attached.** The directive is on the line directly above one statement or
  declaration, and applies to exactly that. This is the usual way to annotate a
  declaration: the declaration keeps its own line and indentation.
- **Slot.** The directive sits at a defined position inside a construct: after
  an `if`, `else if`, or `else` header for one branch, after a `case` or
  `default` test, after `final` in a function declaration, or after a
  parameter's name.

Because a blank line separates unattached from attached, a blank line between
a directive and code changes its meaning. Formatters must preserve the blank
lines around directives.

Each directive accepts only certain kinds of position, listed in the
[catalog](#directive-catalog). Placing a directive where it is not accepted is
an error, so a blank line that accidentally separates an attached-only
directive from its declaration is reported rather than ignored:

```zax
[<inline>]

smallHelper final : ()() = {   // error: inline is unattached here and accepts no unattached position
}
```

## Directive ranges

`deprecate`, panic controls, and `export` can also apply to a run of
declarations. An unattached **state marker** sets the state from that point
onward:

| Marker | State from here on |
| --- | --- |
| `=always` | On: declarations are deprecated or exported; for panic controls, the check is enabled |
| `=never` | Off: declarations are not deprecated or exported; for panic controls, the check is disabled |
| `=inherit` | Back to the state the enclosing opening or file started with |
| `=default` | Back to the language's default |

The state lasts until a later marker for the same directive changes it, or the
region ends. There are no separate "end" markers: a range ends when another
marker sets a different state.

```zax
MyGeometry :: type {

  [<deprecate=always>]

  oldArea final : ()() = {
  }
  oldPerimeter final : ()() = {
  }

  [<deprecate=never>]

  area final : ()() = {
  }

}
```

```zax
MyGeometry :: type {

  [<deprecate=always>]

  oldArea final : ()() = {
  }
  oldPerimeter final : ()() = {
  }

  [<deprecate=never>]

  area final : ()() = {
  }

}
```

`oldArea` and `oldPerimeter` are deprecated; `area` is not.

### What bounds a range

A range never escapes the region it is written in:

- **Openings.** A namespace, type, function body, or other opening starts with
  the state of the code around it. A range inside it ends at its closing brace,
  which restores the outer state.
- **Files.** A range at a file's top level ends at the end of that file. Every
  file starts from its module's state, so moving or reordering files never
  changes what a range covers.
- **Included files.** A file brought in by source inclusion starts with the
  state in effect at the line that includes it. Placing directives before or
  after an inclusion controls what the included file starts with; the included
  file's own ranges still end at its end.
- **Imports.** Directive state never crosses an `import`. An imported module
  starts from the language's defaults, or from a starting state its build
  configuration sets, and then applies its own source, never its importer's
  state.

Because closing braces and file ends restore the previous state, there are no
`push` and `pop` directives.

### State markers and attached forms are different

A state marker is always unattached. The attached form instead applies to one
declaration or statement and has its own spelling: `[<deprecate>]` or
`[<export>]` alone, and `[<panic=yes, ...>]` or `[<panic=no, ...>]` for one
statement's check. Writing a state marker directly above a declaration makes it
look attached, so it is an intent error:

```zax
[<deprecate=always>]
drawLineOld final : ()() = {   // intent error: a state marker placed as an attachment
}
```

Write the attached form, `[<deprecate>]`, to mark one declaration, or put blank
lines around the state marker.

Only `deprecate`, panic controls, and `export` have ranges. Other directives,
such as the [Nothing policy](nothing-instances.md#set-a-scoped-default), attach
to one opening and deliberately have no "from here onward" form.

## What a directive may promise

Every directive is one of three kinds:

| Kind | What it does | Examples |
| --- | --- | --- |
| Hint | Suggests an optimization. A compiler may ignore it, and the program's meaning never changes. | `inline` levels, `likely`, `unlikely` |
| Unchecked promise | States a fact the compiler may rely on. Where a check exists, it is on by default; with the check disabled, breaking the promise is undefined behavior. | `always`, `never`, `void`, disabling a panic category |
| Semantic control | Changes what the program means | `execute`, `constant`, `deprecate`, `export`, the Nothing policy, layout |

An unchecked-promise directive is itself the visible marker of the promise. It
needs no separate `unsafe` enclosure. Whether a check exists is controlled by
its panic category, never by a debug or release mode.

## Directive names

Directives defined by the language have plain names such as `inline`. Names
starting with `x-` are unofficial:

```zax
run final [<x-acme-hot-path>] : ()() = {
}
```

- An unknown plain name is an error, so a misspelled `[<inlien>]` cannot
  silently do nothing.
- An unknown `x-` name may be ignored, optionally with a warning.
- Compilers are recommended to put their own name after `x-`, as in
  `x-acme-`, unless several vendors deliberately share a directive.
- A vendor directive may refine meaning but must never change it. A change of
  meaning needs a new name, with the old one deprecated. Vendors who share
  directives are recommended to keep a registry among themselves. The language
  cannot enforce this.

## Deprecating declarations

`deprecate` marks declarations that callers should stop using. Using a
deprecated declaration produces a deprecation warning:

```zax
[<deprecate>]
drawLineOld final : ()(from : Point, to : Point) = {
}

drawLineOld(start, finish)   // warning: drawLineOld is deprecated
```

Attached to a type or namespace, it covers everything declared inside. As a
[range](#directive-ranges), it covers the declarations between its markers.

Options refine where and how the warning appears:

| Option | Meaning |
| --- | --- |
| `context=import` (default) | Warn for uses from modules that import this one |
| `context=all` | Warn for every use, including this module's own |
| `context=local` | Warn only for uses inside this module |
| `error` | Make the warning an error |
| `min="x.y"` | Warn when an importer requests a version below `x.y` |
| `max="x.y"` | Warn when an importer requests a version above `x.y` |

```zax
[<deprecate, context=all, error>]
legacyChecksum final : (result : U32)(data : Byte[] readonly &) = {
}

[<deprecate, max="1.1">]
classicRenderer final : ()() = {
}
```

`min` and `max` compare against the version an importer requests. Import
versioning itself belongs to future build and dependency design, which may
refine these two options. `context=local` cannot be combined with `min` or
`max`, because those concern importers.

A standalone `[<deprecate>]` with nothing attached is an intent error. A
deprecation inside a [`wellformed{...}`](compile-time-code.md#warnings-and-intent)
check must be explicitly allowed or rejected.

## Source mapping for generated code

Tools that generate Zax from other sources want diagnostics to point at the
original input, not at the generated file. Source-mapping directives say where
the following source came from:

```zax
[<file="inventory.csv">]
[<file, generator="Acme Inventory Generator">]

[<line=21>]

apple : Integer     // reported as inventory.csv line 21
banana : Float      // line 22

[<line=400, increment=0>]

donut : Float       // line 400
eclair : String     // still line 400
```

- `file` names the original input, and `generator` names the tool.
- `line` sets the reported line of the first source line after it that holds
  code. `increment` says how far the reported line advances per physical line;
  `0` keeps reporting the same original line.
- `[<module, location="https://example.com/inventory">]` sets the reported
  location of a generated module.

These are unattached and positional by nature, and that is safe because they
change only what diagnostics and reflection report, never what the program
does. Reading the same information back is done with
[`Compilation`](compile-time-code.md#compilation-information) queries, not
directives.

## Directive catalog

| Directive | Kind | Positions | Defined by |
| --- | --- | --- | --- |
| `execute` | Semantic control | Slot after `final` | [Compile-time code](compile-time-code.md#letting-a-function-decide-execution-policies) |
| `inline` | Hint | Slot after `final` | [Function invocation](function-invocation.md#inlining-hints) |
| `constant` | Semantic control; requires the argument to be a compile-time value and is part of the prototype | Slot after a parameter's name | [Generics](generics.md#compile-time-inputs-in-the-ordinary-input-list) |
| `instantiation-depth` | Semantic control; sets the maximum nesting of a generic's own instantiations, default and guaranteed minimum `4096` | Slot after `final` in a generic declaration | [Generics](generics.md#limits) |
| `likely`, `unlikely` | Hint | `if`/`else` branch slot, `case`/`default` slot | [Core flow control](core-flow-control.md#branch-hints-and-path-promises), [switch](switch.md#case-hints-and-promises) |
| `always`, `never` | Unchecked promise | `if`/`else` branch slot, `case` slot; `never` also unattached | [Core flow control](core-flow-control.md#branch-hints-and-path-promises), [switch](switch.md#case-hints-and-promises) |
| `deprecate` | Semantic control | Attached; range | [This page](#deprecating-declarations) |
| `export` | Semantic control | Attached; range | [Namespaces and modules](namespaces-and-modules.md#exporting-with-directives) |
| Panic controls (`panic`) | Unchecked promise when disabling | Attached; range | [Safety and analysis](safety-and-analysis.md#controlling-panic-checks) |
| `nothing-instance-default` | Semantic control | Attached to a module or namespace opening | [Nothing instances](nothing-instances.md#set-a-scoped-default) |
| `align`, `reserve`, `void` | Semantic control; `void` is also an unchecked promise | Attached to a member; `reserve` unattached in a type body | [Type definitions](type-definitions.md#explicit-member-layout) |
| `reorder` | Semantic control | Attached to a type definition | [Type definitions](type-definitions.md#member-order) |
| `file`, `line`, `module` | Source mapping | Unattached | [This page](#source-mapping-for-generated-code) |

## Diagnostics

Diagnostics should distinguish:

- an unknown plain directive name;
- a directive in a position it does not accept;
- a state marker placed as an attachment, or a standalone directive that needs
  something to attach to;
- a state marker that sets the state already in effect, which is redundant;
- incompatible options in one directive, such as `context=local` with `min`;
  and
- a deprecated use, naming the directive that deprecated it.

## Source stability

The following changes are source- or behavior-visible:

- adding or removing a blank line between a directive and code changes whether
  it is attached;
- moving a declaration into or out of a range changes whether it is
  deprecated or exported;
- moving source inclusion lines relative to directives changes what an
  included file starts with; and
- deprecating a declaration adds warnings for its users, and errors where
  `error` is given or where an undecided `wellformed` check uses it.

Moving whole files, or reordering a module's files, does not change what any
range covers.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar.

Future work owns:

- exact `export`, selective exposure, alias export, and re-export option sets;
- panic category names and their registry;
- source generation directives;
- asynchronous-execution, ABI, and `once`-value directives, together with the
  features they describe;
- import versioning used by `deprecate` `min` and `max`; and
- exact diagnostic identifiers and presentation.

For Zax's accepted foundational direction, see the
[language vision](vision.md).
