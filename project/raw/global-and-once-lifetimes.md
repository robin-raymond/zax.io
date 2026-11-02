# Raw input: global and once lifetimes

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining exact global and lazy-once lifecycle ordering |
| Applies To | Cross-file global construction, dependency ordering, circular initialization, `once`, teardown, and concurrency |
| Owns | Preserved legacy ordering and failure pressure, activation pressure, and retirement criteria |
| Does Not Own | The current process life-path mental model |
| Source / Provenance | Former constructor page, removed from the tree and retained in git history; `basics.md`; work item `014` |

## Current boundary

[Zax lifetimes and references](../../language/lifetimes-and-references.md)
establishes that global instances live in the process life path and are destroyed
in reverse of their established construction order.

Current [declarations](../../language/declarations-and-bindings.md#type-callable-once-functions)
and [invocation](../../language/function-invocation.md#type-and-instance-calls-to-once-functions)
also establish the narrow function rule: a `once` function has one type-owned
implementation and is callable through either the type or an instance.

This file preserves global and `once` **value** lifecycle behavior, plus the
remaining capture, concurrency, teardown, and generic-specialization questions.
It does not reopen the accepted type/instance call surface.

## Ordering pressure

Legacy design proposed:

- declaration/source order as the ordinary global construction order;
- dependency detection that may require one global to initialize before another;
- a diagnostic for circular global initialization dependencies;
- reverse destruction order;
- lazy `once` construction interleaved with global construction history; and
- thread-aware initialization for `once`.

The intended value mechanism is a lazy unique instance for one declared place.
The legacy spelling is illustrative; it is not current declaration syntax:

```zax
// Illustrative legacy form for a future once value.
uniqueId final : (result : Integer)() = {
  value once : Atomic$(Integer)
  return ++value
}
```

The claims attached to that example are the direction future lifecycle work
must preserve while it resolves the open ordering questions below:

- the instance is constructed on first access, not at every call;
- concurrent first access constructs it once;
- allocation uses the accessing thread's context allocator; and
- destruction is interleaved with global destruction in reverse construction
  order.

A `once` function is not this value. Type-callable `once` functions already
have a current call rule and do not memoize a returned instance.

Future work must reconcile those proposals with:

- module/source loading order;
- compile-time versus target execution;
- imported module initialization;
- repeated or failed initialization;
- panic;
- concurrency;
- dynamic library loading;
- and source stability when dependency analysis improves.

No exact ordering rule in this file is accepted.

## Nothing-instance preparation pressure

[Zax Nothing instances](../../language/nothing-instances.md) requires the
applicable Nothing backing to be prepared before any observable access.
Ordinary global startup is the expected common strategy; the language does not
require delayed first-use construction. A type that never needs an observable
vacant-pointer target or receiverless `_` access need not materialize backing.

Future lifecycle work must define:

- how demand dependencies are discovered and ordered across modules;
- startup publication and, only if a future implementation/language policy
  permits lazy preparation, concurrent first demand;
- cycles among custom `+++ final once` Nothing initializers;
- panic, failure, retry, and partial preparation;
- identity per generative module instance and concrete generic specialization;
- destruction of dedicated custom Nothing storage;
- dynamic loading and unloading; and
- whether compatible compiler-provided backing is materialized during startup,
  synthesized without addressable storage, or safely delayed without changing
  observable ordering.

Preparation must complete before publication. It does not invoke an ordinary
zero-input constructor, does not establish automatic process-global uniqueness,
and does not make compiler-provided shared backing an ordinary complete `T`
instance. Custom Nothing mutation remains ordinary shared-state mutation whose
synchronization is the programmer's responsibility.

## Generative module-state pressure

Current [namespace/module design](../../language/namespaces-and-modules.md)
establishes:

- every import creates a distinct module instance;
- each instance has individualized module-global state;
- equal source and injection inputs do not merge that state;
- exact injected namespace aliases may deliberately let several module
  instances use one already imported dependency and its state; and
- there is no automatic super-module global facility.

Future lifecycle work must decide:

- when each generative module instance constructs and destroys its globals;
- how import/source order and detected dependencies interact;
- whether a module instance initializes eagerly, on first reachable use, or
  under another explicit policy;
- how an exactly injected shared dependency is initialized once and ordered
  against each consumer;
- failure, retry, panic, and partial-initialization behavior;
- reverse destruction when several import paths reference one injected shared
  instance; and
- whether a broader shared-state facility is needed after concrete use cases
  that exact injection cannot express.

Cache or generated-code reuse must not merge state. Conversely, different local
aliases to one deliberately injected module instance must not duplicate its
state accidentally.

Current [partials](../../language/partials.md) may add `once` state to a type.
That state belongs to the module instance that declares the partial, not to the
module that declares the type. Future lifecycle work must order its
initialization and teardown under the same module-instance rules.

## Shared array-storage provider pressure

Current array storage direction permits one provider instance to back several
arrays through strong provider participation and independent unique array
handles.

A storage type may expose a receiverless factory:

```zax
MyStorage :: type {
  defaultStorage final : (
    result : MyStorage * strong
  )() unbound = {
    return MyStorage.sharedStorage
  }

  // Illustrative future once-value syntax.
  sharedStorage once : MyStorage * strong
}
```

The `unbound` factory can be called through the storage type. `once` on a
function would not memoize its result. Sharing therefore requires separately
owned persistent provider state, potentially through a future type-owned
`once` value, the execution context, a global owner, or another explicit
lifecycle.

Future work must define:

- construction and publication of the shared provider;
- whether concurrent first use blocks, retries, or panics;
- how provider-factory calls prove stable shared identity;
- provider replacement;
- teardown after arrays and raw provider pointers end;
- module-instance versus process-wide sharing;
- interaction with execution-context replacement; and
- failure while an array requests its unique backing handle.

The provider/handle protocol remains
[indexed array-storage-strategy work](array-storage-strategies.md). This file
owns only the shared provider value's global/`once` lifetime pressure.

## `lock-free` construction pressure

The root legacy compiler-directive page keeps the `lock-free` section and its
example: a local `once` value whose construction is normally guarded against
concurrent first use, with `[<lock-free>]` removing that guard:

```zax
// Legacy evidence; local once values are not current design.
giveMeMyType final : (myType : MyType &)() = {
  singleton once [<lock-free>] : MyType
  return singleton
}
```

Its meaning is already settled for when `once` values are designed: it is an
[unchecked-promise directive](../../language/compiler-directives.md#what-a-directive-may-promise)
promising that construction is never contended, so it needs no separate
`unsafe` marker, and violating the promise is a data race. Exact attachment and
whether the guard is a panic-checkable condition remain open.

## Activation and retirement

Activate this input when global initialization, `once`, module startup, process
teardown, or cross-file lifecycle ordering is reviewed. Move accepted behavior
into the lifecycle and source/module owners, then retire this file.
