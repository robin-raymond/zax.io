# Raw input: assembly and foreign interoperability

| Field | Value |
| --- | --- |
| Status | Raw placeholder / non-authoritative |
| Audience | A future numbered work item defining assembly, FFI, ABI, or binding behavior |
| Applies To | Direct assembly and selected C/C++ interoperability |
| Owns | Preservation of aligned assembly/FFI requirements, scalar ABI boundaries, and legacy native/code-page string view pressure |
| Does Not Own | Accepted foreign layouts, calling conventions, or binding contracts |
| Source / Provenance | Work items `001` and `012`, Zax purpose/design principles, and optional layout/ABI pressure |

## Aligned requirements

- Direct integration with assembly is required.
- Binding to C and C++ is important.
- Interoperability is selective in both directions.
- Zax-native facilities need not all have foreign equivalents.
- Foreign-language facilities need not become Zax concepts.
- C-compatible representations, functions, symbols, and calling conventions are
  likely the most direct boundary.
- Adapters are appropriate when semantic models differ.
- Manual ABI work may remain possible through explicit low-level mechanisms
  without first-class guarantees.

## Representative boundary

C++ virtual functions are not a first-class Zax concept.

A C++ adapter, explicit function table, or manually interpreted known ABI may
bridge a particular case. Direct vtable interpretation is compiler-, ABI-,
layout-, and version-sensitive and belongs at an explicit low-level trust
boundary.

The general pressure is:

> Zax supports selected foreign representations and calling conventions without
> importing the foreign language's complete semantic model.

## Union and variant ABI pressure

Current [Zax unions](../../language/unions.md) define zero-filled, offset-zero,
possibly jagged lenses with passive/all-bit-safe admission and local unsafe lens
proof. Similarity to a C or C++ union does not establish:

- foreign active-member or aliasing rules;
- exact size, padding, alignment, or bit-field behavior;
- pointer or callable representation;
- scalar profile agreement;
- calling-convention classification; or
- stable layout across tools and language versions.

A named foreign-union contract may deliberately choose compatible layout and
unsafe responsibilities. Otherwise use an adapter or explicit low-level view.

Current [Zax variants](../../language/variants.md) define absence plus one named
managed payload without promising a tag width, niche, payload offset, or calling
convention. Future interop must define how foreign tagged unions map names,
unknown tags, absence, payload lifecycle, transfer, destruction, and layout.
Representation coincidence on one target is insufficient.

## Optional representation and ABI pressure

[Zax optional values](../../language/optional-values.md) defines semantic states
without fixing a tag, niche, layout, or calling convention. Future interop work
must decide explicitly:

- whether a selected foreign boundary uses a separate discriminant or a valid
  niche;
- how nested optional states remain distinguishable;
- whether optional pointers/functions preserve their inner vacant or
  unavailable state;
- size, alignment, padding, parameter passing, and result return;
- stability across modules, toolchains, targets, and language versions;
- adapter behavior when a foreign optional/nullability model has fewer states.

Representation coincidence on one target does not make two semantic states or
type layers equivalent. Manual ABI use remains an explicit low-level boundary
until a named contract accepts an exact optional representation.

## Nothing-instance and foreign-nullability pressure

[Zax Nothing instances](../../language/nothing-instances.md) fixes semantic
state without fixing an ABI representation. Future interop work must define:

- how a foreign null pointer maps to a Zax vacant pointer and the destination
  pointee type's Nothing instance;
- whether a foreign function pointer maps to an unavailable Zax function value,
  is rejected, or requires an adapter;
- how safe type-aware adapters remap vacancy while raw foreign and unsafe views
  preserve representation;
- whether trapping or custom Nothing policies may cross a foreign boundary;
- whether the foreign target supplies read/write/execute trap capabilities or
  needs an adapter with a different documented failure boundary;
- pointer width, tags, sentinels, function thunks, and calling-convention
  effects;
- ownership and provenance when foreign code returns or stores a sentinel; and
- diagnostics when the foreign model has fewer states than a Zax optional
  containing a vacant pointer or unavailable function value.

No foreign null address is the universal Zax representation. A named ABI
contract may select one representation for one boundary; adapters remain
required where semantic states differ.

## Untyped pointers and signed address integers

Zax has no `Void` or `Unknown` pointee type; `OpaqueObserver`,
`OpaqueReferenceObserver`, and `OpaqueOwner` erase pointer types inside Zax. A
foreign `void *` still needs a boundary mapping, for example to `OpaqueObserver`,
to a byte pointer, or to an adapter that records the expected type.

Zax also has no signed pointer-representation role such as `IPointer`; addresses
convert to `UPointer`, and differences use `PointerDelta`. C `intptr_t` and C++
ABI signatures that use it need a workaround mapping, such as an equal-width
signed integer at the boundary.

Activate with FFI, binding, or ABI work.

Current
[Zax structural shape and compatibility](../../language/structural-shapes-and-compatibility.md)
defines Zax-internal shape, layout, posture, anchor, and coercive relationships.
None is an ABI or calling-convention promise. Future interop work must name the
additional foreign contract rather than treating safe Zax recasting as foreign
conformance.

## Enum representation and admission pressure

Current [enum behavior](../../language/enums.md) fixes an immediate integer
backing and explicit admission boundary without promising a foreign ABI.
Interop work must decide:

- layout and calling-convention guarantees for exact and profile-selected
  backing roles;
- how foreign unknown values enter strict, relaxed, and flags enums;
- whether adapters validate, preserve, or reject unknown values;
- how enum aliases, defaults, and flags masks appear in generated bindings; and
- whether persistent or foreign formats require explicit member values and
  fixed exact backing.

## Scalar format and ABI pressure

Current [endianness](../../language/endianness.md),
[fixed-point scalars](../../language/fixed-point-scalars.md), and
[binary floating-point scalars](../../language/floating-point-scalars.md) fix
Zax scalar meaning without promising one foreign ABI.

Future interop work must distinguish:

- intrinsic logical format width from ABI argument/result slots;
- scalar storage-envelope padding from foreign container or stack padding;
- active-environment aliases from absolute-endian formats;
- numeric conversion from byte-preserving exchange;
- profile-selected `Float` identity from one foreign `float`/`double` ABI type;
- X87Extended80's ten-byte intrinsic encoding from twelve- or sixteen-byte ABI
  placement;
- MBF40 numeric conversion from preservation of its historical five-byte file
  form;
- OFP8/BF16 hardware support from calling-convention support; and
- ordinary target layout from a stable serialization or wire contract.

Equal Zax scalar format and structural compatibility do not establish foreign
calling convention, register class, stack alignment, NaN payload transport, or
padding guarantees. A named foreign contract or adapter remains required.

## Legacy string and character pressure

Current [strings and characters](../../language/strings-and-characters.md)
defines:

- always-defined profile-selected `Legacy.Char` and optional
  `Legacy.WChar` identities, each with profile-selected signedness;
- length-tracked `Legacy.CharString`, optional `Legacy.WideString`, and MBCS
  strings with no implied sentinel;
- policy-specialized terminated char/wide strings;
- policy-owned terminator, in-band, contiguity, suffix, and admission rules; and
- conceptual `.c_str()`-style access only for applicable NUL policies.

Those language-level value guarantees do not establish a foreign ABI. Future
interop work must decide:

- exact pointer/reference shape and const/writable authority;
- contiguous layout and element ABI;
- lifetime, pinning, and invalidation;
- target versus compiler-host native character profiles;
- encoding and code-page identity at the call boundary;
- ownership and deallocation of returned foreign strings;
- validation after writable or unsafe foreign calls;
- adapter behavior for foreign inputs containing embedded NUL; and
- whether code-page conversion occurs before, during, or outside the call.

A trailing NUL policy prevents missing-terminator access but does not grant
calling convention, symbol, allocator, or lifetime compatibility.

MBCS encoding identity is independent of C-string termination. A C-facing MBCS
boundary needs an explicit adapter or terminated policy specialization; an
ordinary `MbcsString<Encoding>` may contain embedded NUL and cannot expose
`.c_str()`.

## ABI directive pressure

The root legacy compiler-directive page keeps the `abi` directive section
(`virtual`, `c`, `never`, `interrupt`) as deferred input. Two observations to
carry into ABI work:

- `never` (a function that never returns) and `interrupt` (an interrupt-return
  sequence) are not calling conventions in the same sense as `c` or `virtual`,
  and may belong in separate directives; and
- `abi=never` collides in name with the
  [`[<never>]` path promise](../../language/core-flow-control.md#branch-hints-and-path-promises).

## Activation and retirement

Activate this input before defining assembly integration, FFI, ABI controls,
binding behavior, X87 correspondence, MBF file exchange, or stable scalar
serialization. Consume its findings through that work and retire or archive
this placeholder afterward.
