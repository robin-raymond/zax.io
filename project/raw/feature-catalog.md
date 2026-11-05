# Raw input: legacy feature catalog

| Field | Value |
| --- | --- |
| Status | Raw legacy inventory / non-authoritative |
| Audience | Future numbered work inventorying or refining Zax concepts |
| Applies To | Feature claims formerly presented on the website entry point |
| Owns | Preservation of the legacy catalog for later disposition |
| Does Not Own | Accepted feature commitments, semantics, or documentation routes |
| Source / Provenance | Legacy `index.md` feature catalog |

## Reading posture

This catalog preserves proposed capabilities without accepting them. Each item
must be evaluated through its applicable concept owner or future numbered work.

Do not use this file as onboarding or as evidence that a feature is committed.

## Preserved catalog

### Low-level features

- Raw memory access
- Raw memory casting
- Bitwise operations
- Sized types and alignment
- Manual memory allocation and deallocation
- Memory clear and memory copy
- Custom allocators
- Collective allocation and deallocation
- Raw pointers and references
- Uninitialized memory type control
- Direct type casting
- Allocation versus initialization control
- Union types
- Structure-of-arrays and array-of-structures support

### Type and flow-control features

- Familiar flow controls
  - `if`, `else`
  - `switch`, `case`, `default`
  - `while`, `until`
  - `redo while`, `redo until`
  - `each in`
  - `each from`
- Scope control and logic grouping with `break` and `continue`
- Arrays
- Explicitly sized integers, floating points, enums, booleans, and strings
- Enums with underlying types
- Locally defined variables
- Locally defined types

### High-level features

- Sized arrays, including strings
- `unique`, `own`, `strong`, `weak`, `handle`, `hint`, `discard`, and `collect`
  memory policies
- Default type initialization
- Optional function arguments
- Multiple function results
- Polymorphic types and functions
- Mutable and immutable types
- Operator overloading
- Constructors and destructors
- Type default-value initialization, including allocation
- Lambda functions with capture
- Member functions
- Namespace import control
- Source-repository module imports
- Shared module compilation control
- Source preservation through asset and module management
- Library and compiler separation
- Type composition with inner and outer casting
- Safe conversion using `as`
- Selective runtime type information
- End-of-scope `defer` execution
- Private and non-exported types
- Type and variable aliases
- Partial types
- Anonymous types
- `nothing` type instances
- Value polymorphism for functions

### Asynchronous programming

- Asynchronous function calling
- Parallel and sequential allocators
- Lazy type evaluation
- Promises
- Tasks
- Coroutines

### Metaprogramming

- Enum metadata
- Compile-time `if` and `else`
- Custom compile-time literals
- Variadic functions
- Explicit meta-function parameters
- Compile-time reflection
- Compile-time execution
- Compile-time generation
- Compile-time checking
- Compile-time type metadata and traits
- Language-integrated build control

### Other proposed features

- Usage deprecation
- No header files
- Runtime panics with selective disabling
- Harmonized warning-suppression mechanisms across compilers

## Recorded dispositions

Focused construction, invocation, and core-flow work has dispositioned these
catalog entries:

| Catalog input | Disposition |
| --- | --- |
| Constructors and destructors | Current conceptual behavior is owned by [Zax construction, replacement, and destruction](../../language/construction-and-destruction.md) |
| Default type initialization | Declaration forms are owned by [Zax declarations and bindings](../../language/declarations-and-bindings.md); member and lifecycle behavior is owned by the construction owner |
| Direct type casting | Current integer conversion is owned by [Zax integers](../../language/integers.md) and the [integer operator catalog](../../language/integer-operator-catalog.md); outer casting is owned by [Zax composition](../../language/composition.md); safe structural recasting, compatible same-storage views, and coercive layout conversion are owned by [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md); programmer-declared `as`, `unsafe cast`, and pointer/integer conversion are owned by [Zax conversions and casts](../../language/casting.md) |
| Allocation versus initialization control | Storage-versus-value lifecycle separation is owned by the construction owner; declaration-bound allocation, pointer results, policy, and arena selection are owned by [Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md) |
| Uninitialized memory type control | Zax has no declaration form that leaves storage indeterminate. Members and result slots may start empty and are constructed with `.=`, `+++`, or an `opaque-construction` assertion, owned by the [construction owner](../../language/construction-and-destruction.md#manual-and-delayed-construction); locals always initialize, and the wrapper-type pattern is taught by [declarations and bindings](../../language/declarations-and-bindings.md#low-level-initialization-belongs-in-a-constructor) |
| Arrays and slices | Fixed and resizable arrays, array expressions, usable capacity, programmer-visible storage selection, indexing, slicing, element places, joining, and intrinsic traversal participation are owned by [Zax arrays and slices](../../language/arrays-and-slices.md); exact provider/handle contracts remain in [array storage strategies](array-storage-strategies.md) |
| Structure-of-arrays and array-of-structures support | Arrays remain atomic structural leaves; conversion between AoS and SoA is explicit transformation owned jointly by [arrays and slices](../../language/arrays-and-slices.md#structural-and-reflection-boundaries) and [structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md#arrays-remain-structural-leaves) |
| Optional function arguments | Current conceptual behavior is owned by [Zax function invocation](../../language/function-invocation.md) |
| Multiple function results | Current result slots, completion, acknowledgement, and routing are owned by the function-invocation owner |
| Polymorphic functions | Current fixed-arity callable viability and partial-order preference are owned by the function-invocation owner; generic functions, operators, and specialization ranking are owned by [Zax generics](../../language/generics.md); value polymorphism, variadics, and operator-specific lookup remain future work |
| Variadic functions | Remain future work, with the legacy root `variadic.md` page as input. Compile-time pressure to preserve: the legacy `print final : ()([<compile>] ...) = { }` form required every variadic argument to be a compile-time value. Under current design each such argument would be a `[<constant>]` input, and the function would be processed once per distinct argument set, as [Zax generics](../../language/generics.md#when-code-is-processed-once-per-input) defines |
| Member functions | Current receiver-call behavior, including type/instance calls to one shared `once` function implementation, is owned by [function invocation](../../language/function-invocation.md); declaration and generated replacement behavior by [declarations and bindings](../../language/declarations-and-bindings.md); receiver qualification by [Zax qualifiers](../../language/qualifiers.md); global/`once` value lifetime, capture, concurrency, teardown, and generic specialization remain future work |
| Result discard acknowledgement | Caller acknowledgement and omission are owned by [function invocation](../../language/function-invocation.md); unread named bindings are owned by [declarations and bindings](../../language/declarations-and-bindings.md#names-the-body-may-leave-unread); allocation `discard` is superseded by `AllocationDisposition.DeferredRecovery`; type-level discard was rejected |
| Harmonized warning-suppression mechanisms across compilers | Preserved for future work in [raw analysis-control input](analysis-controls.md) |
| Familiar flow controls (`if`, `else`, `while`, `until`, `redo while`, `redo until`) | Current conceptual behavior, including `forever` and explicit `scope`, is owned by [Zax core flow control](../../language/core-flow-control.md) |
| Scope control and logic grouping with `break` and `continue` | Current `break`, `continue`, `next`, `goto`, and flow-label targeting are owned by [Zax core flow control](../../language/core-flow-control.md) |
| `switch`, `case`, `default` | Current ordered runtime behavior is owned by [Zax switch, case, and default](../../language/switch.md); generalized destructuring and payload binding remain in [raw pattern-matching input](pattern-matching.md) |
| `each in`, `each from` | Current direct traversal, cursor protocol, active-`each` access, progression, and erasure are owned by [Zax iteration](../../language/iteration.md); intrinsic array/slice participation is owned by [arrays and slices](../../language/arrays-and-slices.md); generic constraints and reflection payloads remain in their focused raw owners |
| Operator overloading | The shared model is owned by [Zax operators](../../language/operators.md), word-spelled forms by [operator phrases](../../language/operator-phrases.md), exact forms by the [operator catalog](../../language/operator-catalog.md), literal declarations/merge/join by [literal source and operators](../../language/literal-source-and-operators.md), tree-pattern behavior by [mixfix operators](../../language/mixfix-operators.md), array index/splice/join behavior by [arrays and slices](../../language/arrays-and-slices.md), and composition exposure by [Zax composition](../../language/composition.md); remaining numeric-family, pointer, non-array runtime-join, and generic mechanics are routed to focused live inputs |
| Bitwise operations | Exact forms and precedence are owned by the [operator catalog](../../language/operator-catalog.md), with complete protected integer behavior in the [integer operator catalog](../../language/integer-operator-catalog.md); integer bit indexing and its no-implied-proxy boundary are owned by [arrays and slices](../../language/arrays-and-slices.md#indexing-element-places), while deferred multiword/reversal/extraction behavior remains in [numeric type families](numeric-type-families.md) |
| Explicitly sized integers, floating points, enums, booleans, and strings | Current finite integer types are owned by [Zax integers](../../language/integers.md), bounded fixed-point formats by [fixed-point scalars](../../language/fixed-point-scalars.md), ordinary/profile/legacy binary floating formats by [floating-point scalars](../../language/floating-point-scalars.md), string/character identities by [strings and characters](../../language/strings-and-characters.md), intrinsic byte order by [endianness](../../language/endianness.md), and enum behavior by [Zax enums](../../language/enums.md); unbounded/custom numeric mechanics remain in [numeric type families](numeric-type-families.md), while runtime string remainder is in [string runtime and encoding](string-runtime-and-encoding.md) |
| Enums with underlying types | Current enum identity, members, admission, operations, flags, and string behavior are owned by [Zax enums](../../language/enums.md); exact generated forms by the [operator catalog](../../language/operator-catalog.md); intrinsic endian scalars are no longer enum types |
| Enum metadata | Narrow generated enum strings and member-iteration requirements are owned by [Zax enums](../../language/enums.md); general metadata shape remains in [reflection](reflection.md) |
| Polymorphic types | Generic types, unions, variants, and identity types, constraints, and specializations are owned by [Zax generics](../../language/generics.md); generated type families, generic aliases, enums, and partials remain in [type parameters and generics](type-parameters-and-generics.md) |
| Explicit meta-function parameters | `$` compile-time parameters, `$(...)` supply, worked-out arguments, and `[<constant>]` inputs are owned by [Zax generics](../../language/generics.md); the local type-parameter-slot rule stays with [Zax function invocation](../../language/function-invocation.md) and [Zax declarations and bindings](../../language/declarations-and-bindings.md) |
| Compile-time execution | Owned by [compile-time code](../../language/compile-time-code.md), with literal-specific behavior in [literal source and operators](../../language/literal-source-and-operators.md); sandboxing and capabilities remain future compile-time design |
| Compile-time `if` and `else`, compile-time generation, compile-time checking | `if compile`, `switch compile`, and `wellformed{...}` are owned by [compile-time code](../../language/compile-time-code.md); source generation remains legacy input in the root compiler-directive page |
| Compile-time reflection, compile-time type metadata and traits | Routed to [reflection](reflection.md); the current protected query forms are recorded by the [operator catalog](../../language/operator-catalog.md#protected-forms) |
| Custom compile-time literals | Current source, declarations, payload merge, value join, generic concrete-result specialization, radix catalogs, and diagnostics are owned by [literal source and operators](../../language/literal-source-and-operators.md); general compile-time and generic mechanics remain in their focused raw owners |
| Selective runtime type information | Routed to [reflection](reflection.md) |
| Safe conversion using `as` | Current integer exact/optional/narrowing conversion is taught by [Zax integers](../../language/integers.md#integer-identity-types), complete protected behavior by the [integer operator catalog](../../language/integer-operator-catalog.md#integer-conversion-and-admission), identity projection/admission by [Zax identity types](../../language/identity-types.md), structural shape/layout conversion by [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md), programmer-declared `as`, `unsafe cast`, and pointer/integer conversion by [Zax conversions and casts](../../language/casting.md), and exact forms by the [operator catalog](../../language/operator-catalog.md#conversion-and-admission-forms) |
| Type and variable aliases | Transparent aliases and explicit representation-related identities are owned by [Zax identity types](../../language/identity-types.md); generic alias receivers and structural identity remain future work |
| Type composition with inner and outer casting | Current named containment, independent `own`, `preferred`, and `expose`, fulfillment, and immediate outer casting are owned by [Zax composition](../../language/composition.md); owning interior pointers through `inner` are owned by [Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md#interior-pointers); identity boundaries remain with [Zax identity types](../../language/identity-types.md); structural equivalence, anchored recasting, and same-storage compatible views are owned by [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md) |
| Anonymous types | Current anonymous structural identity, recomposition, transformation, and compiler-hidden arithmetic-report behavior are owned by [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md); generic and reflection exposure remains in their focused raw owners |
| Lambda functions with capture | Current lambda expressions, capture, callable storage, partial application, composition, chaining, and weak invocation are owned by [Zax lambdas and callable composition](../../language/lambdas-and-callable-composition.md); optional combinator, restricted-scope, generic, mixfix, and reflection pressure remains in [callable-adjacent raw input](function-composition-and-chaining.md) |
| Partial types | Current partials, visibility grants, `seal` categories, added storage, and hook points are owned by [Zax partials](../../language/partials.md); compile-time hook detection is owned by [partials](../../language/partials.md#asking-at-compile-time-whether-a-hook-is-fulfilled); how generic bodies find helpers is owned by [Zax generics](../../language/generics.md#how-names-inside-a-generic-body-are-found); generic and generated partial pressure remains in [type parameters and generics](type-parameters-and-generics.md) |
| Library and compiler separation | Required language support, including the protected `Boolean` operations, is distinguished from optional library APIs by the [Zax language vision](../../language/vision.md) and [Zax operators](../../language/operators.md); toolchain, helper-linkage, and incomplete-toolchain pressure is preserved in [raw build and dependency input](build-and-dependencies.md) |
| Value polymorphism for functions | Remains future value-polymorphism work; core flow constrains only the conditional expression's per-operation convergence |

These dispositions do not accept the remaining catalog items.

## Activation and retirement

Use this file when inventorying existing concepts or checking whether legacy
feature claims have received dispositions. Retire it after every retained item
has an accepted owner, explicit deferral, or discarded status.
