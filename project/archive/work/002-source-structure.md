# 002: Source and statement structure

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `002` |
| Created | 2026-08-19 |
| Completed | 2026-08-20 |
| Owns | The historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current source-structure design, formal grammar, parser behavior, or current project guidance |

## Non-authority notice

This archived file is a historical collaborative working record. Its statements,
observations, proposals, and aligned findings remain non-authoritative. Current
source-structure design is owned by the promoted language document identified in
the closure result below.

## Fixed initiating input

This section records the information known and aligned when work item `002` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for how Zax source is divided
and read before reviewing declarations, types, expressions, or detailed flow
semantics.

The review must determine how:

- whitespace and newlines affect source;
- statement boundaries are recognized;
- blocks and single-statement bodies are formed;
- the current semicolon-as-composition behavior works and whether it should
  remain;
- source continues across lines;
- ordinary, nested, and documentation comments behave; and
- these choices affect readability, formatting, parsing, highlighting, and
  diagnostics.

Use the review to identify one lasting source-structure owner and its boundary
with flow control, declarations, expressions, and tooling.

### Motivating pressure

Every Zax example depends on the source-structure model, but the current basic
parsing material is embedded in a large legacy page.

The current design also gives familiar punctuation unfamiliar behavior:

- newlines appear to separate statements;
- semicolons combine statements rather than terminate them;
- combined statements may form one flow-control body;
- backslashes explicitly continue source across lines; and
- braces group statements.

If this foundation changes after other concept pages are promoted, examples and
explanations throughout the language will require avoidable revision.

This concern directly tests the accepted vision principles that familiar forms
should be preferred unless divergence earns its cost, ordinary code should
remain readable, and advanced behavior should not become cryptic.

### Known assumptions

- The current [language vision](../../../language/vision.md) is accepted
  foundational direction.
- Human-developer readability takes priority over preserving legacy syntax
  merely because it is already documented.
- Existing parsing and flow-control pages are legacy design input rather than
  automatic authority.
- The review should establish a programmer-facing mental model before formal
  grammar or parser implementation.
- Formatting and tooling consequences are part of the language-design pressure
  when they affect readability or reliable interpretation.
- Material consequences outside the initial boundary must be captured and
  dispositioned rather than ignored.

### Known inclusions

- The meaning of a statement.
- Whether and when newlines are semantically meaningful.
- Whitespace between tokens and statements.
- Block formation with braces.
- Single-statement flow-control bodies insofar as their extent depends on source
  structure.
- The purpose, behavior, readability, and necessity of semicolon composition.
- Explicit line continuation and whether it remains necessary.
- Single-line, multiline, nested, and documentation comments.
- Ambiguity involving indentation, line breaks, braces, or nested control flow.
- Formatter, syntax-highlighting, parser, and diagnostic implications.
- Candidate owner and reading-path boundaries.

### Known starting boundaries

- Declaration and binding operators such as `:`, `:=`, and `::`.
- Expression precedence and associativity.
- Literal values, interpolation, and character encodings.
- Type inference or structural typing.
- Function signature and result semantics.
- Detailed behavior of `if`, loops, `switch`, scopes, or other control forms
  beyond identifying the extent of their statement bodies.
- Formal grammar, parser algorithms, AST structure, or compiler implementation.
- Broad rewriting of language examples before source structure is aligned.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Whether newlines are statement delimiters, separators, or contextual syntax.
- Whether semicolon composition provides enough value to justify unfamiliar
  behavior.
- Whether explicit line continuation is necessary or can be inferred safely.
- Whether braces should be optional for one-statement bodies.
- Whether indentation has any semantic role or only formatting significance.
- Which nested-comment and documentation-comment forms are worth retaining.
- Whether documentation comments belong entirely in source structure or partly
  in a later documentation-generation concern.
- Whether the lasting owner should be a focused source-structure document or a
  cohesive portion of a broader language-basics owner.

### Initial stopping guidance

Stop when the work has:

- established an aligned programmer-facing model for statements and blocks;
- dispositioned semicolon composition and explicit line continuation;
- established the aligned comment model at the necessary conceptual depth;
- identified immediate formatting, parser, highlighting, and diagnostic
  consequences;
- identified one plausible lasting owner and its boundaries;
- captured declaration, expression, detailed flow, and tooling questions for
  later work; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples, archive this work item, or
begin work item `003` without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides the accepted
  readability, familiarity, visibility, and elegance principles.
- [Basics: basic parsing](../../../basics.md#basics-of-parsing) - contains the
  current statement, semicolon, continuation, and comment proposals.
- [Flow control: `if`](../../../flow-control.md#if) - demonstrates how line breaks,
  braces, and semicolon composition currently determine a control body. Initial
  reading should stop before the later `if` subsections unless discussion
  requires them.

### Consequence-driven

- Read a focused section of [scope](../../../scope.md) only when block or scope
  boundaries become relevant beyond ordinary braces.
- Read other flow-control examples only when a concrete source-structure
  ambiguity requires comparison.
- Read declaration, function, operator, or literal material only when the source
  model cannot be evaluated without crossing that boundary.
- Read a specific file in `project/raw/` when a material consequence crosses its
  deferred concern.
- Propose an update or new raw input when a material finding must be preserved
  for future work. Discussion, alignment, and explicit edit authorization still
  apply.
- Inspect focused Git history only when the origin or former purpose of an
  unusual syntax choice becomes consequential.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work item `001`.

Audit-only material requires direct language-maintainer instruction or a
specific audit or recovery need.

## Working record

### Aligned findings

The findings in this section are aligned for the current review scope. They
remain non-authoritative working material until a separately discussed, aligned,
and authorized promotion incorporates them into lasting owner documentation.

#### Programmer-facing source model

At statement level, an uncontinued newline ends a simple statement. This is a
logical source rule rather than a claim that every physical line contains
exactly one complete construct.

Newlines may occur without ending the surrounding statement when an expression
delimiter such as `(` or `[` remains open. The exact catalog and behavior of
expression forms belongs with their later reviews, but ordinary delimited
multiline layout must not require an explicit continuation on every line.

Outside such an open construct, `\` explicitly suppresses one
otherwise-significant newline and continues the same statement across physical
lines:

````zax
value = 1 + 2 + 3 + \
        1000 + 1001
````

Zax does not infer continuation merely because an operator lacks an operand.
Requiring `\` in that situation keeps a missing term from silently consuming the
next physical line.

`\` must be the last non-comment code token on its physical line. An ordinary
`//` comment may follow it:

````zax
value = calculate() + \ // Explain the continued expression.
        more()
````

The next physical line must be indented beyond the structural indentation of
the statement being continued. Its exact expression alignment may vary:

````zax
value = calculate() + \
        more()

value = calculate() + \
                   more()
````

An unindented continuation contradicts its explicit membership in the statement
and is an error:

````zax
value = calculate() + \
more() // error: continuation is not indented beyond the statement
````

Every physical line crossed by explicit continuation must visibly carry that
intent. A continued line may contribute statement tokens, end with another
`\`, or consist only of an appropriately indented `\`:

````zax
value = 1 + 2 \
        + 3 + \
        4

value = 1 + 2 + \
        \
        3
````

A continuation-only line may have an ordinary line comment after `\`. A blank
or comment-only line does not carry continuation and is an error:

````zax
value = 1 + 2 + \

        3 // error: blank line broke the continuation
````

````zax
value = 1 + 2 + \
        // error: comment-only line does not carry continuation
        3
````

The continued physical lines must eventually form one valid statement. A
continuation at end of file is an error. The source before `\` need not be
incomplete in isolation when the explicitly joined result is valid:

````zax
value = 1 + 2 \
        + 3
````

`///` may not follow `\` because no complete construct exists there to receive
documentation. Documentation may appear on the physical line where the complete
multiline construct ends.

#### Effective statements, bodies, and scope

Keep these source structures distinct:

- a **simple statement** performs one ordinary statement-level operation;
- a **composed statement** combines complete statements with `;`;
- a **block statement** contains statements within `{` and `}`; and
- a **flow-control body** consumes one effective statement, which may have any
  of the preceding forms.

A flow-control clause establishes a scope around its complete body regardless
of the body's source form. Assuming `x` is introduced by the examples below,
`x` is unavailable after the `if` in every case:

````zax
if true
    x = 3
doSomethingElse(x) // error: x is outside its clause scope
````

````zax
if true
    x = 3;
    doSomething(x)
doSomethingElse(x) // error: x is outside its clause scope
````

````zax
if true {
    x = 3
    doSomething(x)
}
doSomethingElse(x) // error: x is outside its clause scope
````

Operands of a composed body share the clause scope, so a name introduced by an
earlier operand may be used by a later operand. Whether a braced body also has a
separately observable nested scope is deferred until lifetime, destruction, and
scope-exit behavior make that distinction relevant. Braces do not make
body-local names escape.

#### Semicolon composition

`;` is a statement-composition operator rather than a statement terminator. It
requires a complete statement on both sides and produces one effective
statement.

Horizontal composition permits several statements on one physical line:

````zax
doA(); doB(); doC()
````

The final statement has no trailing semicolon because there is no further
operand.

Vertical composition extends a one-statement position across adjacent
statement-start lines:

````zax
if true
    doA();
    doB();
    doC()
````

Each vertically composed operand must have structural indentation consistent
with belonging to the same body. Leading, doubled, and trailing semicolons are
errors because they have missing operands:

````zax
; doA()       // error: missing left operand
doA();; doB() // error: missing middle operand
doA();        // error: missing right operand
````

Vertical composition is for extending a position that otherwise accepts one
statement. It is an error where the surrounding source structure already
accepts vertically separated sibling statements:

````zax
if true {
    doA();
    doB() // error: vertical composition is redundant in a block
}
````

Horizontal composition remains legal where a newline is not available to
separate statements:

````zax
if true {
    doA(); doB()
}
````

Comment trivia may appear between vertically composed operands without
requiring `\`:

````zax
if true
    doA(); // The semicolon is source, before the line comment.
    // Explain why the next operation belongs to this body.
    doB()
````

A semicolon inside a line comment remains comment text. An empty physical line
may not separate vertically composed operands because it visually contradicts
their composition:

````zax
if true
    doA();

    doB() // error: blank line within vertical composition
````

Nested unbraced control flow exposes the level at which an operator can be
written:

````zax
if outer
    if inner
        doA();
        doB()
````

Here `doA()` and `doB()` compose as the inner body. When the complete inner
control statement must instead be composed with another statement in the outer
body, braces are required to give the complete inner statement an explicit end
at which `;` can be written:

````zax
if outer
    if inner {
        doA()
    };
    doB()
````

Without braces, a semicolon after the inner body composes at the inner level.
Indenting its right operand as an outer-body statement contradicts that
structure:

````zax
if outer
    if inner
        doA();
    doB() // error: semicolon composition and indentation disagree
````

Omitting both the braces and semicolon also fails to express a second statement
in the outer one-statement body:

````zax
if outer
    if inner
        doA()
    doB() // error: outer body membership is not expressed by syntax
````

Indentation validates the explicitly represented operator level; it does not
choose that level.

Semicolon composition intentionally differs from C in a one-statement body:

````zax
if condition
    doA(); doB()
````

Both calls belong to the Zax `if`. The visually similar C form executes only its
first call conditionally. Mandatory layout validation mitigates mistakes in
vertical composition, but it cannot remove this same-line semantic difference.
Human-facing documentation must teach it directly.

#### Mandatory layout validation

Grammar and explicit tokens determine source structure. Indentation does not
select body membership, but it must truthfully represent the structure already
determined. Contradictory indentation is a non-disableable compile error rather
than an advisory lint or a silently reformatted condition.

For example:

````zax
if true
    doA()
    doB() // error: indentation claims body membership not expressed by syntax
````

Removing a required composition operator therefore produces an error rather
than silently moving `doB()` outside the body. Adding an operator while
dedenting its intended operand is also an error:

````zax
if true
    doA();
doB() // error: composition and structural indentation disagree
````

Structural indentation and continuation alignment are different concerns. A
statement-start line must occur at the indentation expected for its parsed
position. A line already identified as a continuation by `\` or an open
expression delimiter may use reasonable visual alignment rather than one exact
expression column. An explicit continuation line must nevertheless be indented
beyond the structural indentation of the statement it continues:

````zax
if outer
    if inner
        value = 1 + 2 + calculate() + \
                                more()

if outer
    if inner
        value = 1 + 2 + calculate() + \
            more()
````

Both continuation layouts may be legal. Without `\`, the same additional
indentation begins an unexpected statement and must produce a focused
diagnostic. A formatter may normalize accepted source, but it must not be the
mechanism that rescues structurally misleading source.

#### Comment model

Comment delimiters do not cross lexical modes. At ordinary source level, the
recognized form determines how its contents are scanned:

- `///` begins documentation text through the end of its physical line;
- `//` begins an ordinary comment through the end of its physical line;
- `/*` followed immediately by one or more `#` characters begins a raw block
  comment; and
- an otherwise ordinary `/*` begins a nestable block comment.

The more specific documentation and raw forms are recognized before their
ordinary line-comment and block-comment prefixes.

Once `//` or `///` begins, every apparent comment delimiter later on that
physical line is ordinary or documentation payload respectively:

````zax
/// Documentation text /* remains documentation through this */ text.
// Ordinary comment text /* does not open a block comment.
````

An apparent block opener in either line-oriented form cannot affect later
physical lines. A subsequent `*/` is therefore an unmatched closer and an error:

````zax
/// The following /* is documentation payload.
doSomething()
*/ // error: no block comment was opened
````

`/*` and `*/` delimit ordinary block comments. Ordinary block comments nest so
that otherwise valid copied source containing balanced block comments can be
commented as a unit:

````zax
/*
    copiedCode()
    /* An existing nested comment. */
    moreCopiedCode()
*/
````

Inside an ordinary block comment, `//` and `///` are inert comment text. Another
ordinary `/*` increases nesting depth, and `*/` decreases it. These rules apply
even when the sequences appear inside copied text that would otherwise be a
string or another language construct.

A raw opener is also recognized inside an ordinary block comment at any nesting
depth. The scanner enters raw mode before treating the opener as another
ordinary nested comment, ignores everything through its exact raw closer, and
then returns to the same ordinary nesting depth:

````zax
/*
    /*
        /*##
        Everything here is raw, including /*, */, ///, and /*# #*/.
        ##*/
    */
*/
````

Commenting arbitrary or malformed source verbatim requires this raw form rather
than language-sensitive interpretation inside ordinary comments.

A raw block comment closes only with a contiguous run of exactly the same number
of `#` characters followed immediately by `*/`:

````zax
/*#
Arbitrary content containing /*, */, "*/", or malformed source.
#*/
````

The delimiter can be lengthened when its closing sequence appears in the
content:

````zax
/*###
Content containing #*/ and ##*/ remains opaque.
###*/
````

Raw-comment contents recognize no nested comments, strings, escapes,
documentation, or other source forms. A raw comment does not itself nest; an
apparent raw opener inside it is content. A fixed repeated `#` marker is
preferred over arbitrary punctuation or names because it gives the same
unbounded delimiter choice with a clear boundary, established counted-delimiter
precedent, and simpler human and tool matching. This contextual use does not
displace the legacy candidate use of `#` as an ordinary-language construct; that
role remains subject to its own future review.

##### Documentation grouping and payload

Adjacent full-line `///` comments at the same structural indentation form one
documentation block. Their line boundaries are preserved rather than
concatenated away. An empty documentation line is represented by `///` and
remains part of the block:

````zax
/// First paragraph.
///
/// Second paragraph.
````

A physically blank line ends the block.

Everything after the recognized `///` marker is opaque documentation payload.
Apparent ordinary or block-comment delimiters inside it are not stripped or
interpreted by Zax:

````zax
/// A URL such as https://zax.io and /* example delimiters */ remain payload.
````

Zax does not prescribe XML, Markdown, or another language for this payload.
Projects, documentation generators, Zax build-time code, AI tools, or other
consumers may interpret it. Content interpretation must not determine whether
text is documentation; detecting XML may affect a chosen tool but not source
classification.

`///` appearing inside an ordinary or raw block comment has no documentation
meaning and requires no attachment.

##### Documentation attachment

Every documentation block has a deterministic target. It attaches either to a
source construct or, in a permitted file-level position, to the source file.
Unattributed documentation is an error.

A leading documentation block attaches forward to the following construct at
the same structural indentation:

````zax
/// Documentation for doSomething.
doSomething()
````

Documentation is prefix metadata rather than a statement. It does not consume a
flow-control body position:

````zax
if true
    /// Documentation for the body statement.
    doSomething()
````

Misaligned documentation is an error:

````zax
if true
/// error: documentation does not match the body statement's indentation
    doSomething()
````

Ordinary comment trivia may intervene between leading documentation and its
target without becoming documentation:

````zax
/// Documentation for doSomething.
// An ordinary source note.
doSomething()
````

A physically blank line breaks forward attachment. In the middle of a file,
this leaves the documentation unattributed and is an error:

````zax
/// Unattributed documentation.

doSomething()
````

A top-level documentation block separated from the first code construct by a
blank line attaches to the source file as preamble documentation. Top-level
documentation after the last code construct attaches to the file as epilogue
documentation. Several separately grouped file-level blocks may attach to the
file in source order. File-level attachment is not inferred for an otherwise
unattributed block in the middle of source.

An inline trailing documentation block attaches backward to the complete
effective statement or construct at the structural indentation represented by
that line:

````zax
output : Integer /// Documentation for output.
````

Attachment does not bubble outward merely because completing an inner construct
also completes one or more enclosing constructs. In this example, the
documentation attaches to the body statement rather than the `if`:

````zax
if true
    doSomething() /// Documentation for doSomething.
````

Leading documentation at the outer structural indentation may independently
attach to the `if`, so both of these blocks are legal and have distinct targets:

````zax
/// Documentation for the if.
if true
    doSomething() /// Documentation for doSomething.
````

A trailing documentation block after a braced body instead appears at the
outer structural indentation and attaches to the complete `if`:

````zax
if true {
    doSomething()
} /// Documentation for the if.
````

For a vertically composed body, trailing documentation on the final operand
attaches to the complete effective composed statement at that body indentation,
not to the enclosing control statement:

````zax
if true
    doA();
    doB() /// Documentation for the complete composed body.
````

The target must be complete. A control header, open expression, or pending
statement-composition operator cannot receive trailing documentation:

````zax
if condition /// error: the if construct is incomplete
    doSomething()

doA(); /// error: the composition operator still requires its right operand
doB()
````

Likewise, `///` cannot follow `\`. It may appear where the complete continued
construct ends:

````zax
value = 1 + 2 + \ // Ordinary commentary is legal here.
        3 /// Documentation for the complete assignment.
````

A non-file construct may receive one directly attached documentation block,
using either leading or trailing placement but not both:

````zax
/// error: output also has trailing documentation
output : Integer /// Competing documentation for output.
````

The same duplicate rule applies independently at each structural level:

````zax
if true
    /// Leading documentation for doSomething.
    doSomething() /// error: duplicate documentation for doSomething
````

````zax
/// Leading documentation for the if.
if true {
    doSomething()
} /// error: duplicate documentation for the if
````

Several adjacent `///` lines are one block. Distinct leading blocks separated by
ordinary comment trivia remain distinct; if they target the same construct,
they produce the same duplicate-attachment error. An internal documentation
separation is instead expressed with an empty `///` line.

#### Immediate tooling and diagnostic consequences

- Tokenization must preserve significant statement-level newlines, explicit
  `\` continuation, open-delimiter continuation, comment trivia, lexical comment
  modes, nested ordinary block comments, and counted raw-comment fences.
- Parsing can determine statement and body structure without using indentation
  as a block grammar.
- Mandatory layout validation compares statement-start indentation with the
  parsed structure, requires hanging indentation for explicit continuation, and
  permits controlled alignment within that constraint.
- Formatting must render simple, composed, and block bodies distinctly and
  must not silently repair invalid structural indentation.
- Syntax highlighting must distinguish ordinary, nested, raw, and documentation
  comments, respect the active lexical mode, and match counted raw-comment
  delimiters.
- Deterministic source representations must preserve documentation-block line
  boundaries and attachment to the applicable source construct or file.
- Diagnostics should identify the local contradiction and, where applicable,
  distinguish a missing `\`, missing `;`, missing braces, unexpected
  indentation, missing semicolon operand, broken continuation, unmatched comment
  delimiter, unattributed documentation, or duplicate documentation attachment.

These are programmer-visible design pressures, not implementation mappings or
parser architecture decisions.

#### Candidate lasting owner and boundaries

A focused human-developer-facing source-structure owner is the leading
candidate. The concern is foundational enough to require a stable reading path
and cohesive enough not to remain embedded in the large legacy basics page.

That owner would explain newlines, continuation, effective statements, blocks,
body extent, mandatory layout validation, and comment forms. It would not own:

- declaration or binding operators;
- compiler-directive placement, attachment, or scoped effect;
- expression precedence, associativity, or the complete delimiter catalog;
- detailed semantics of individual flow-control constructs;
- lifetime and scope-exit behavior beyond the source-visible clause boundary;
- documentation payload languages or generator behavior;
- formal grammar, parser architecture, formatter implementation, or highlighting
  implementation.

Those adjacent owners may rely on the source-structure model and show local
examples without redefining it.

### Review status and pre-promotion boundary

The aligned model now addresses the fixed initiating concern at the conceptual
depth needed for maintainer review. No known unresolved contradiction prevents a
pre-promotion documentation fit dry run after that review.

The exact multiline behavior of individual `(`, `[`, and other expression forms
remains with their later owners. This work establishes only the constraint that
an open expression delimiter may support ordinary multiline layout without `\`
and that its layout must remain truthful. Exact declaration syntax,
compiler-directive placement and attachment, expression semantics, detailed
flow behavior, scope-exit effects, and documentation payload interpretation
likewise remain later-owner questions unless a promotion dry run exposes a
direct ownership conflict.

At the time this pre-promotion status was recorded, no finding in this working
record had been promoted. The later authorized promotion and closure result is
recorded at the end of this file.

## Dispositions and promotion dry run

### Dry-run status

This is the read-only documentation fit dry run required before promotion. It
records proposed lasting ownership and an exact promotion change set for
discussion. It does not promote the aligned findings or authorize any listed
change.

The dry run found no unresolved conceptual contradiction in the aligned model.
It did find legacy documentation that must be reconciled in the same promotion
so accepted source structure does not coexist with conflicting definitions,
warning classifications, or examples.

### Focused reading and audits performed

The dry run expanded beyond the initial reading scope only where promotion
created a concrete documentation consequence:

- [Website entry point](../../../index.md) and [repository entry point](../../../README.md)
  were checked for the public route to an accepted owner.
- The [language vision](../../../language/vision.md) was checked because its maturity
  wording currently describes the repository before a second accepted
  conceptual owner exists.
- [Basics](../../../basics.md) was checked beyond its parsing section for the
  operator catalog and every explicit-continuation example.
- [Flow control](../../../flow-control.md) was checked for local source-structure
  definitions and every Zax example ending a physical line with `;`.
- [Scope](../../../scope.md) was checked to establish the boundary between source
  blocks, clause scope, and still-legacy lifetime and scope-exit claims.
- [Compiler warnings and errors](../../../warnings-errors.md) was checked because
  mandatory source errors currently appear as configurable warning entries.
- [Compiler directives](../../../compiler-directives.md),
  [namespacing](../../../namespacing.md), and the other root language pages were
  checked to distinguish genuine statement continuation from legacy uses of
  `\` as apparent directive attachment.
- The [raw input index](../../raw/README.md) and the focused
  [legacy feature catalog](../../raw/feature-catalog.md) were checked for a
  source-structure claim requiring disposition.

Operating-prompt sources and archive contents were not read. No historical
recovery or implementation mapping was needed.

The source-example audits found:

- no vertically composed Zax example that redundantly uses `;` where its
  surrounding source structure already accepts vertically separated siblings;
- no semicolon example that contradicts the aligned operand or body-extent
  rules;
- eighteen examples that use `\` to relate a compiler directive to nearby code
  rather than to continue one statement: nine in `compiler-directives.md`,
  seven in `namespacing.md`, and two in `warnings-errors.md`; and
- two warning-registry entries that conflict with mandatory source validity:
  `statement-separator-operator-redundant` and
  `newline-after-continuation`.

### Proposed ownership map

| Finding or material | Proposed lasting disposition |
| --- | --- |
| Significant statement-level newlines and the ordinary one-line mental model | One new accepted owner at `language/source-structure.md` |
| Explicit `\` continuation, hanging indentation, continuation-only lines, comments, and failure cases | `language/source-structure.md` |
| Simple, composed, block, and effective statements | `language/source-structure.md` |
| Horizontal and vertical `;` composition, operand requirements, redundancy errors, and nested-control brace pressure | `language/source-structure.md` |
| Mandatory, non-disableable layout validation and its distinction from structural grammar | `language/source-structure.md` |
| Ordinary line comments, nested ordinary block comments, counted raw block comments, and lexical-mode priority | `language/source-structure.md` |
| Documentation grouping, opaque payload, leading and trailing attachment, file attachment, structural-level targeting, and attachment errors | `language/source-structure.md` |
| Clause-owned body scope and the fact that body-local names do not escape | `language/source-structure.md` at the source-visible depth required to explain the three body forms |
| Exact scope nesting, destruction, lifetime, and scope-exit effects | Explicitly deferred to a later scope and lifetime owner; legacy `scope.md` remains input rather than authority |
| Exact multiline behavior of `(`, `[`, literals, and other individual expression forms | Explicitly deferred to their expression or literal owners, constrained by the accepted general source model |
| Compiler-directive placement, attachment, inline use, and scoped or persistent effect | Explicitly deferred to later compiler-directive design; adjacency examples remain legacy input and do not use `\` merely to express attachment |
| `;;` sub-statement syntax in control headers | Not owned by this work and not inferred from `;`; remains legacy flow-control input for later review |
| Exact `if`, loop, `switch`, `using`, and other control semantics | Remain with later flow-control review; local examples may rely on accepted body formation without redefining it |
| Documentation payload language and rendering | No language-defined payload format; project tools, build-time code, or other consumers may define interpretations |
| Exact diagnostic identifiers, severity registry, and presentation | Future diagnostics owner; the source owner establishes only which conditions are mandatory errors and what a useful diagnostic distinguishes |
| Parser, formatter, highlighter, source-model, and compiler architecture | Implementation pressure only; no implementation owner is created in this repository |

The raw feature catalog contains no source-structure claim to consume or
relocate. It remains temporary raw input for later feature-by-feature
disposition. No other raw placeholder crosses this work item's promotion
boundary, so no raw file moves or retires in this change set.

### Proposed minimal structure and reading path

The smallest coherent structure is:

```text
language/
    vision.md
    source-structure.md
```

No new directory family or `language/README.md` is needed. The public
`index.md` remains the router. It would distinguish current conceptual design
from legacy topic pages and route readers directly to both accepted owners:

```text
README.md
    -> index.md
        -> language/vision.md
        -> language/source-structure.md
        -> legacy root topic pages
```

`language/source-structure.md` would be a human-developer-facing concept owner,
not a grammar or specification. Its metadata would identify current conceptual
design, unestablished implementation state, owned source concerns, and the
adjacent concerns it does not own.

No existing language page moves. `basics.md`, `flow-control.md`, `scope.md`, and
the other root pages remain legacy design input. Focused links from affected
legacy pages point to the accepted owner without making the owner depend on
legacy material.

### Existing-file fit and required promotion effects

| File | Proposed promotion effect |
| --- | --- |
| `language/source-structure.md` | Create the one lasting owner and incorporate every aligned source-structure finding at human-developer-facing depth, including costs, errors, examples, and explicit boundaries |
| `index.md` | Add a current conceptual-design route to source structure, stop describing the vision as the only current owner, and keep root topic pages visibly legacy |
| `language/vision.md` | Narrow its maturity statement from all detailed language pages to the pre-existing root legacy pages so the new accepted owner is not mislabeled |
| `basics.md` | Replace the competing “Basics of parsing” content with a concise router to the accepted owner while retaining the useful heading/anchor; remove the legacy XML detection and `///*** ... ***///` forms; update the local `;` and `\` operator labels to accepted terminology; leave `;;` and literal-specific semantics visibly legacy |
| `flow-control.md` | Replace its top-level source-body definition with a concise link and local summary that relies on the accepted owner; retain conforming local flow examples and keep exact control semantics legacy |
| `scope.md` | Add a focused boundary note linking accepted block and body formation while leaving detailed scope, lifetime, destruction, `break`, and `continue` behavior legacy |
| `warnings-errors.md` | Remove the two conflicting source conditions from the configurable warning registry, defer exact diagnostic identifiers, add the accepted-owner boundary, and remove `\` from the two legacy directive-adjacency examples while restoring ordinary indentation |
| `compiler-directives.md` | Remove `\` from nine directive-adjacency examples and restore ordinary indentation; preserve the examples as legacy input without defining attachment semantics |
| `namespacing.md` | Remove `\` from seven directive-adjacency examples and restore ordinary indentation; leave the genuine multiline hash continuation unchanged and do not otherwise promote namespacing semantics |

The repository `README.md`, project guidance, raw index, active-work index, and
other root language pages require no promotion edit. Their current routes or
examples either remain correctly bounded or already conform to the aligned
source model.

The retained `basics.md#basics-of-parsing` heading is a useful local router for
readers of the legacy basics page and preserves the active work item's focused
reference while `002` remains current. It is not a compatibility-only stub or a
second definition.

### Exact proposed promotion change set

Subject to maintainer review, alignment, and separate edit authorization, the
promotion would contain exactly these lasting-document changes:

1. Create `language/source-structure.md` as the accepted conceptual owner.
2. Update `index.md` to route current conceptual design and legacy notes
   separately.
3. Update the maturity wording in `language/vision.md`.
4. Replace the competing parsing material and two operator labels in
   `basics.md` as described above.
5. Add the accepted source-structure boundary and link in `flow-control.md`.
6. Add the accepted source-structure boundary and link in `scope.md`.
7. Reconcile the warning registry, boundary wording, and two continuation
   misuses in `warnings-errors.md`.
8. Remove statement continuation from nine directive-adjacency examples in
   `compiler-directives.md`.
9. Remove statement continuation from seven directive-adjacency examples in
   `namespacing.md`.

No move, archive operation, active-work index update, raw-file change,
operating-prompt change, specification scaffold, implementation document, or
compiler source belongs in this promotion.

Archiving `002`, preparing `003`, and updating `project/README.md` are closure
and continuation actions after successful promotion and validation. They require
their own discussion, alignment, and authorization.

### Proposed promotion validation

After an authorized promotion, validation should confirm:

- `language/source-structure.md` is the only current owner of the promoted
  source concerns and does not depend on active or archived work for meaning;
- its status and implementation state do not imply a specification or compiler;
- the public index reaches both accepted concept owners and still labels root
  topic pages as legacy;
- `basics.md`, `flow-control.md`, `scope.md`, and `warnings-errors.md` link to
  rather than compete with the source owner;
- no current page retains XML detection, the `///*** ... ***///` form, or the
  old semicolon-separator description as current source behavior;
- every Zax example with explicit `\` continuation has a nonblank,
  non-comment-only next line that is hanging-indented relative to the continued
  statement, unless that line is an explicitly hanging-indented
  continuation-only `\` line;
- compiler-directive adjacency examples do not use `\` merely to express an
  undefined attachment relationship;
- every vertically composed Zax example occurs in a one-statement position and
  has a right operand at matching structural indentation;
- the two source-validity conditions no longer appear as configurable warnings;
- `;;` has not been silently accepted, redefined, or conflated with `;`;
- local links and the retained basics heading anchor resolve;
- Markdown headings, tables, and code fences render coherently;
- the final changed-file set matches the separately authorized nine-file
  promotion scope; and
- the maintainer's staged and unstaged comparison boundary remains unchanged
  until separately authorized.

### Promotion and closure result

On 2026-08-20, the language maintainer authorized the dry-run change set and its
later directive-continuation correction. Promotion:

- created [Zax source structure](../../../language/source-structure.md) as the
  current conceptual owner;
- updated the public index and language-vision maturity boundary;
- converted the competing basics parsing section into a focused route;
- added source-owner boundaries to the legacy flow, scope, and warning pages;
- reconciled mandatory source errors with the legacy warning registry;
- removed `\` from legacy directive-adjacency examples without defining their
  future attachment semantics; and
- validated current links, Markdown structure, continuation layout, semicolon
  composition examples, owner independence, and the authorized promotion scope.

Every material finding received one of these dispositions:

- promoted source behavior is owned by `language/source-structure.md`;
- exact declaration, binding, initialization, and assignment behavior initiates
  work item `003`;
- exact expression-delimiter, literal, flow-control, `;;`, scope-exit, lifetime,
  documentation-payload, diagnostics, and implementation concerns remain
  explicitly deferred to later owners; and
- no raw input required relocation or retirement.

This file is now historical, non-normative, and audit-only. Current
documentation does not depend on it for meaning.
