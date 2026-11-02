
# [Zax Programming Language](index.md)

## Basics

### Basics of parsing

Accepted source reading and layout are defined by
[Zax source structure](language/source-structure.md). It owns statement-level
newlines, explicit continuation, simple and composed statements, block and body
formation, mandatory layout validation, and ordinary, raw, and documentation
comments.

Accepted value declarations, initialization, binding visibility, qualifier
axes, name resolution, and assignment boundaries are defined by
[Zax declarations and bindings](language/declarations-and-bindings.md).

Current module roots, namespaces, imports, injection, visibility defaults, and
forward integration are defined by
[Zax namespaces and modules](language/namespaces-and-modules.md).

The accepted rule for when a spelling counts as a keyword is defined by
[Zax source structure](language/source-structure.md#contextual-keyword-recognition),
and the term itself by
[Zax language-design terms](language/terms.md#contextual-keyword).

This legacy basics page no longer defines those concerns. The remaining sections
preserve unreviewed design input for keywords, detailed operators, types,
and related concepts. Current literal source and string/character behavior is
routed explicitly below.


### Keywords

Keywords are simple words that a compiler will intrinsically understands. Keywords are either a single word or a compound set of words. When a compound set of words is used then all of the words must be present in the correct sequence of the keyword is not matched.

````zax
alias
alias type
atomic
await
break
case
case continue
catch
channel
continue
readonly
copy
deep
default
defer
discard
each / from
each / in
except
false
forever
forward enum
forward operator literal
forward module
forward namespace
forward reshape
forward type
forward union
forward variable
if
if / else
immutable
import
namespace
writable
last
lazy
move
mutable
mutator
once
operator binary
operator literal
operator pre unary
operator post unary
override
own
unsafe pliable
private
promise
raw
redo until
redo while
+++ replacement
return
scope
shareable
strong
switch
task
true
type
union
unique
unpliable
until
using
varying
weak
while
yield
yield suspend
````

This remains a legacy keyword inventory rather than an authoritative catalog.
Current pointer ownership uses `unique`, `shareable`, `strong`, `weak`, and
pointer-layer `atomic`; interior pointers are ordinary `strong` or `weak`
pointers formed with `inner`. The former pointer meanings of `own`,
`handle`, `hint`, `lease`, and `collect` are superseded; any future `own` use for
composition is a separate unresolved concept.


#### Keyword disambiguation

The current rule is owned by
[Zax source structure](language/source-structure.md#contextual-keyword-recognition).
It defines contextual keyword recognition, postfix `_` for one word, and strict
`bare{...}` neutralization for one complete source unit. Keyword aliases and
compound-keyword replacements are not part of current Zax.


### Operators

> This flat list is retained only as legacy evidence. The current programmer
> model is [Zax operators](language/operators.md), exact current forms are in the
> [operator catalog](language/operator-catalog.md), and tree-pattern behavior is
> in [mixfix operators](language/mixfix-operators.md). Literal source and
> declarations are current in
> [literal source and operators](language/literal-source-and-operators.md).
> Remaining allocation, pointer, and metaprogramming entries below are legacy
> evidence; the list does not make them current or available.

#### Literal-operator disposition

Current quote, prefix, merge, join, declaration, compile-time, numeric catalog,
and raw-pattern behavior is defined by
[Zax literal source and literal operators](language/literal-source-and-operators.md).
A separated single quote remains the
[exact phrase fence](language/operator-phrases.md#exact-phrase-fencing).

````
"                  // ordinary String shorthand
prefix'            // attached literal payload
'                  // separated phrase fence, not a literal
````


#### Historical non-overloadable proposals

The following unresolved forms remain evidence for pointer, allocation,
composition, chaining, and result-shape work. Their legacy classifications are
not current guarantees:

````
*                   // post-unary pointer type declaration
&                   // post-unary reference type declaration, or
                    // pre-unary capture by reference operator
.                   // post-unary dereference operator
.                   // binary namespace resolution operator
>>                  // binary function composition
|>                  // binary function invocation chaining
->                  // post-unary argument combine operator (combine remaining
                    // function result arguments into a single automatically
                    // defined type)
<-                  // pre-unary argument split operator (split type into
                    // multiple function arguments)
````

Retained function composition now uses `>|>`, leaving `>>` for shifts, and
immediate chaining uses `|>`; both are defined by
[Zax lambdas and callable composition](language/lambdas-and-callable-composition.md#compose-functions-for-later-use).

Allocation now uses the declaration-bound `@`, `@!`, `@<`, and `@!<` family
defined by
[Zax pointers, allocation, and arenas](language/pointers-and-arenas.md#allocation-forms).
Legacy parallel `@@` is superseded because concurrency is an arena capability.
Legacy synchronous `@!` is superseded; current `@!` reports allocation failure
through the destination role's vacant pointer state.


#### Remaining historical operator-phrase input

Operator phrases are current design and taught by
[Zax operator phrases](language/operator-phrases.md). Exact forms and precedence
are in the [operator catalog](language/operator-catalog.md#operator-forms).

The list below retains only the unresolved variadic proposal. Its exact words
are not protected because variadic functions have not established their
prototypes. Current container/member navigation, interior ownership, and pointer
queries are owned by [Zax composition](language/composition.md) and
[Zax pointers, allocation, and arenas](language/pointers-and-arenas.md).

````
count of             // pre-unary count of a variadic expression
````

A compiler has both a host and a target for compilation. Any compile-time code that evaluates on a compiler's host system may have different value sizing and alignments than that of a compiler's target system. For example, a host may operate on a 64-bit system but target may compile for a 32-bit compilation.

A context sensitive version of sizing exist as follows (where the sizing and alignment is determined by code evaluating at compile-time or runtime context):

````
size of
alignment of
offset of
overhead size of
is constant
````

A `host` version of byte sizing operators exist as follows (whose meaning mirrors the version with a `host` prefix):

````
host size of
host alignment of
host offset of
host overhead size of
is host constant
````

A `target` version of byte sizing operators exist as follows (whose meaning mirrors the version with a `target` prefix):

````
target size of
target alignment of
target offset of
target overhead size of
is target constant
````

> The `host`/`target` prefixed forms above are legacy input. Current design asks
> for a specific environment through environment-relative type names, such as
> `size of Target.Integer` and `size of CompilerHost.Integer`; unqualified
> queries answer in the active environment. See
> [Zax compile-time code](language/compile-time-code.md#which-definitions-apply-while-compiling)
> and [Zax endianness](language/endianness.md#active-and-absolute-names).


#### Other expressions

The promoted declaration, discard, call/index, block, construction, directive,
current-instance, and lifecycle forms were removed from this legacy list. The
remaining generic/variadic spellings are future evidence:

````
$               // pre-unary templated argument declaration
...             // unary variadic values (array of optional variable arguments)
$...            // unary variadic types (array of types of variadic values)
````


### Compiler directives

````
abi                 // forces a function on a `type` to use a specific ABI
                    // calling convention
align               // align contained types to a zero modulus address boundary
asset               // copy asset to built bundle
asynchronous        // indicates a function not normally considered to operate
                    // asynchronously may be performed asynchronously
compilation         // controls if the compilation context is the host or the
                    // target for system specific intrinsic sizing and endian
                    // encoding
compile             // a value must be defined as a compile-time constant
compiles            // if a code block that follows compiles then a `true` is
                    // replaced otherwise a `false` is replaced
concept             // declare a function as a compile-time check for
                    // input/output argument type checks within a meta-function
deprecate           // declare API sections as being deprecated
error               // cause an error in compilation
execute             // evaluates code blocks at compile-time
export              // make type, variable, and other declarations visible to
                    // module importation
inline              // tells compiler to generate a function call as inline code
                    // rather than as a call to function
likely              // indicates a code path is more likely to be executed (for
                    // compiler and CPU optimization)
location            // the code URL location as a string literal
lock-free           // disable lock generation around `once` values
panic               // control panic behavior in code generation
reserve             // reserve non-accessible unused space in a type
source              // loads a related source file
void                // declared a contained value occupies a location within a
                    // `type` without allocating space for the contained value
resolve             // controls when a declaration should resolve
tab-stop            // sets the tab-stop for the source that follows
time                // time of compile as a string literal
unlikely            // indicates a code paths is less likely to execute (for
                    // compiler and CPU optimization)
warning             // display a warning or control compiler warning behaviors
````

#### Compiler literal directives

````
compiler                 // compiler related literals
file                     // indicates the source name being compiled
function                 // current function being compiled
line                     // source line being compiled
module                   // module related literals
````


### Recommended naming conventions

````zax
// variables are recommended in lower camelCase and type names are recommended
// in upper CamelCase
variableName : TypeName

// functions are recommended in lower camelCase
funcName : ()() = {
    // ...
}

// function prototypes are recommended in upper CamelCase
FunctionPrototype :: alias type ()()

// scope names are recommended in lower_case_with_underscores
scope my_scope {
}

// namespaces are recommended in upper CamelCase
variableName : MyModuleName.SubType

// enum names and enum values are recommended in upper CamelCase
Fruit :: enum {
    Apple,
    Banana
}

// false abbreviations are highly discouraged
notGood : WrdsNotKnwnAbbr
vecList : VecIsShortForVector
````


### Type declaration

````zax
// Use Pascal style variable declaration where the variable name is
// specified followed by the type
variableName : TypeName

// Types are declared using the `type` keyword
TypeName :: type {
    variableName : Integer
}

// A `type` can be assumed based on a value rather than requiring explicit
// declaration (note: two unique operators are below; the `:` and the `=`
// are not the same operator)
assumedType := funcReturningType()

// types can be deduced based on automatic type deduction from a variable
// instead of an explicit `type` name
originValue : Integer
valueBorrowsOriginalValuesType : originalValue

// symbols that start with _ are reserved for compiler and toolchain generated
// symbols and may contain additional underscores where needed (thus are
// entirely reserved and must not be used by a programmer as a prefix)
_reservedVariableName
_ReservedTypeName

// symbols ending with _ are discouraged except in the case of disambiguating
// keywords and word operators from symbols (and a compiler may lint these
// names)
as_ // e.g. `as` is a keyword, but `as_` is not a keyword.

MyType :: type {
    m_no := 0   // NOT recommended as this Zax is a data oriented and not an
                // object or classification oriented language with members

    _no := 0    // NOT recommended (Zax has built in disambiguation)
    no_ := 0    // NOT recommended (Zax has built in disambiguation)

    // This pointer for the current `type`'s instance is reserved as a single
    // underscore `_` and `type` variables can be distinguished from arguments
    // by using `_.value` versus a locally declaration of `value`.

    value : Integer

    function final : ()() = {
        value : Integer

        _.value = 1    // `MyType`'s `value` is set to `1`
        value = 1      // local `value` is set to `1`
    }
}
````


### Intrinsic types

> **Current integer disposition.** Fundamental finite integer types, canonical
> names and namespaces, role identities, exact/optional/narrowing conversion,
> representation, counts, sizes, and portability are now owned by
> [Zax integers](language/integers.md). Transparent aliases and explicit
> identity declarations using `admit`/`restricted` and `expose`/`opaque` are
> owned by
> [Zax identity types](language/identity-types.md).
>
> The promoted integer catalog has been removed from this legacy page rather
> than retained as duplicate teaching.
>
> **Fixed and floating disposition.** Current fixed-point types, names,
> arithmetic, conversion, and representation are owned by
> [Zax fixed-point scalars](language/fixed-point-scalars.md). Current exact,
> profile-selected, OFP8, and legacy binary floating formats are owned by
> [Zax binary floating-point scalars](language/floating-point-scalars.md).
> Intrinsic byte order is owned by
> [Zax endianness](language/endianness.md). The superseded `F16`/`F32` catalog,
> minimum-width `Half`/`Single` interpretation, and width-only `Float` generic
> have been consumed rather than retained as duplicate teaching.
>
> The remaining block preserves unresolved non-numeric intrinsic input. String,
> character, Unicode, and legacy-encoding behavior has moved to
> [Zax strings and characters](language/strings-and-characters.md).

````zax
boolean : Boolean   // A value representing `true` or `false` literals
````

Type-erased pointers use `OpaqueObserver`, `OpaqueReferenceObserver`, and
`OpaqueOwner`, defined by
[Zax pointers, allocation, and arenas](language/pointers-and-arenas.md#type-erased-ownership-and-observation).

There is no universal `Nothing` type. Per-type Nothing instances and vacant
pointers are defined by
[Zax Nothing instances](language/nothing-instances.md).


### Intrinsic system literals

Current unprefixed integer behavior is owned by
[Zax integer literals and realization](language/integer-literals.md). Attached
single-quote versus separated phrase-fence tokenization is owned by
[Zax source structure](language/source-structure.md#phrase-fences-and-literal-coordination).
Ordinary real/string source, literal operators, numeric bases, merge, joining,
and raw patterns are current in
[Zax literal source and literal operators](language/literal-source-and-operators.md).
String/character identities, escapes, encodings, and directional joins are
current in
[Zax strings and characters](language/strings-and-characters.md).

### Intrinsic Namespaces

`Module` is the protected name of the current module instance's root. `Scalars`
and protected short built-in names are available automatically; independent
libraries still require import. Complete current behavior is defined by
[Zax namespaces and modules](language/namespaces-and-modules.md).


### Hello World

Zax does not require or intrinsically recognize a function named `main`.
Application source at global scope forms the program's global execution path.
This legacy target-selection spelling makes the intended execution environment
explicit:

````zax
:: import Module.System.Standard.Io

runApplication final [<execute=target>] : ()() = {
    out.writeLine("Hello world!")
}

runApplication()
````

Global declarations and expressions form one conceptual global execution path.
Global instances live within the process life path and are destroyed in reverse
of their established construction order when that path ends. Exact cross-module
and dependency ordering remains future work. See
[Zax lifetimes and references](language/lifetimes-and-references.md#common-life-paths).

Command-line arguments, environment variables, exit status, and similar startup
facilities are platform-library behavior rather than intrinsic parameters of a
language-defined `main`.

Complete compile-time versus target execution selection remains legacy
compiler-directive input; the function name has no entry-point significance.
