# Zax execution context

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers using thread-scoped services and context-provided defaults |
| Applies To | The `___` execution context, its application-wide shape, per-thread instance, replacement, default arenas, and default array-storage selection; not a formal grammar or runtime ABI |
| Implementation State | Not established by this repository |
| Owns | The programmer-facing execution-context mental model and its relationship to allocation and array-storage defaults |
| Does Not Own | Allocation syntax and arena behavior ([pointers, allocation, and arenas](pointers-and-arenas.md)); general partial behavior ([partials](partials.md)); async context switching; arena interfaces; task-local context; or implementation transport |
| Source / Provenance | Legacy context input reconciled with current allocation, lifetime, and concurrency design |
| Supersedes | The retired root context page |

## One context shape, one current instance per thread

`___` names the current execution context for a thread's execution path.
Conceptually:

```zax
currentThread.___
```

Functions reached along that path can use the same current context without
receiving an ordinary explicit parameter at each call.

The context's type is `ExecutionContext`. The complete application has one
resolved context shape:

- the language supplies a core shape;
- modules may add their own members and functions through partials;
- the complete shape is fixed before runtime use; and
- every replacement context instance has that same shape.

## Adding your own context state

A module adds per-thread state by declaring a [partial](partials.md) on
`ExecutionContext`:

```zax
MyRequestTracking :: partial ExecutionContext {
  requestId : U64 = 0

  nextRequest final : (result : U64)() writable = {
    ++_.requestId
    return _.requestId
  }
}
```

Code that grants the partial can then use it through `___`:

```zax
handleRequest final : ()() = {
  Module.MyRequestTracking :: expose partial

  id := ___.nextRequest()
}
```

Every thread's context carries `requestId`, but only code that grants
`MyRequestTracking` can name it. Another module can add its own `requestId` in
its own partial without conflict, because each is visible only where its partial
is granted.

The compiler constructs every addition when a thread's context comes online, so
a context partial establishes its storage with a no-argument `+++` or member
initializers, like any other partial.

`ExecutionContext` accepts storage from any module's own source, which ordinary
types do not. As a result, its complete shape is known only when the whole
application is assembled. That is acceptable here because the runtime constructs
every context instance and code normally reaches it through `___`. A type that
embedded an `ExecutionContext` by value would inherit the same pending layout.
The general cost is described under
[storage from any module](partials.md#storage-from-any-module).

## The context during compilation

Compile-time code runs on the build machine, in a separate copy of the program
with its own globals and its own `___`. That copy cannot wait for the whole
application to be assembled, so its context shape is fixed at each point where
compile-time code runs. It includes the core members, partials from completed
imports, and this module's partials completed before that point. Nothing in the
compile-time context reaches the running program's context. The complete rules
are described by
[compile-time code](compile-time-code.md#the-compile-time-___).

## Replacing the current context

A thread may replace its current context:

```zax
currentThread.___ = myReplacementContext
```

`myReplacementContext` must have the application's complete context shape.
Replacement changes what later context-dependent operations observe on that
thread. It does not mutate the shape or retroactively change operations that
already selected and captured a context value.

A module cannot construct additions it cannot name, so a replacement context
normally starts as a copy of the current one:

```zax
restartRequests final : ()() = {
  Module.MyRequestTracking :: expose partial

  myReplacement := currentThread.___       // the generated copy carries every addition
  myReplacement.requestId = 0              // change only what this code can name
  currentThread.___ = myReplacement
}
```

The context's generated copy and `=` include every module's additions. A
partial may declare its own same-type `=` on `ExecutionContext`. That is legal
but not recommended: it is ambiguous with the generated `=` wherever the
partial is granted, and it is not guaranteed to remain permitted.

The in-place context instance need not be internally synchronized merely because
the application has several threads. Each thread has its own current context.
Objects stored in or reached through the context still require whatever
thread-safety, affinity, and sharing contracts their own use demands.

Async tasks, suspension, migration, inheritance into new threads, and restoration
of prior context instances remain future concurrency work. Such work must
preserve well-defined context changes rather than accidentally using whichever
thread resumes a task.

## Default arenas

The core context shape provides access to:

- the default object arena; and
- the default detached control-block arena.

Exact member or function names are not yet established. Conceptually they serve
the roles illustrated by:

```zax
// Illustrative names, not accepted declarations.
___.getDefaultArena()
___.getDefaultControlBlockArena()
```

An allocation with no explicit object arena reads the current context's default
object arena when that allocation operation executes:

```zax
owner : MyValue * unique = @
```

A detached control block with no explicit control-block arena similarly uses the
current context's default:

```zax
owner : MyValue * strong =
  @{ control: ControlBlockPlacement.Detached }
```

An explicit arena overrides only that allocation:

```zax
owner : MyValue * unique = @{ myArena }
```

The complete allocation forms, defaults, ordering, failure, ownership, and arena
requirements are defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocate-through-a-declaration).

## Default array storage

An array declaration with no explicit provider resolves a storage
representation profile before its owning layout and size are fixed. When
that profile is provider-backed, construction may ask the current context for a
compatible provider instance:

```zax
values : Integer[0..]
```

The request identifies the element slot size/alignment, dimensions and size
limits, initial count, and any required storage capabilities. The context may
return strong access to a compatible shared provider or another declared
provider default whose costs remain visible.

Runtime context replacement cannot change one already resolved owning
representation from inline to provider-backed or otherwise change its
self-contained size. Inline versus provider-backed representation is resolved
before runtime provider-instance selection.

An explicitly supplied provider or provider type overrides this selection:

```zax
provider : MyStorage * strong

byInstance : Integer[50] storage provider
byType : Integer[50] storage type MyStorage
```

A shared provider still gives each array one unique backing handle. The
provider's own backing arenas remain its concern; selecting an array provider
does not silently replace the context's default object or control-block arenas.

Exact context hooks, storage-provider factories, capability request shape,
shared-provider initialization, and teardown remain future work. Current
programmer-visible storage selection is explained by
[Zax arrays and slices](arrays-and-slices.md#storage-strategies).

## Nested allocations

An arena selected for one allocation does not become an implicit default for
allocations performed while constructing that resident instance:

```zax
Parent :: type {
  child : Child * unique = @
}

parent : Parent * unique =
  @{ arena: scratchArena } [{}]
```

`parent` uses `scratchArena`. `child` consults the current `___` when its own
allocation executes.

When nested allocation must use a particular arena, pass that arena through the
constructor or supply a preallocated value through the construction packet. This
keeps the dependency visible and also works when the containing instance uses
stack or global storage rather than an outer dynamic allocation.

Ordinary callees likewise observe the thread's current context. An enclosing
allocation's explicit arena, failure form, disposition, collection policy, and
control placement do not silently rewrite allocations performed by those
callees.

## Costs and diagnostics

Using a context-provided default introduces a dependency on the current thread
context even though source omits an explicit arena or storage operand. Tooling
should expose which context member supplied an effective default.

Representative diagnostics include:

- replacement context has the wrong resolved shape;
- required default arena is unavailable;
- no default array storage satisfies the requested shape/capabilities;
- selected context service is incompatible with the current thread;
- context replacement would violate a live operation's captured dependency; and
- async or cross-thread code has no defined context-transfer behavior.

## Boundaries and maturity

This document is current conceptual design, not a context ABI or implementation
mapping.

Still deferred:

- exact core context member names, and the namespace that holds
  `ExecutionContext`;
- carrying context additions across async suspension;
- initialization and teardown order;
- context inheritance when creating a thread;
- async task capture, replacement, restoration, and migration;
- task-local or nested context layers;
- reflection; and
- implementation transport, including whether calls physically receive a hidden
  parameter.
