# Raw input: asynchronous execution

| Field | Value |
| --- | --- |
| Status | Raw placeholder / non-authoritative |
| Audience | A future numbered work item defining async language behavior |
| Applies To | Async I/O, coroutines, concurrency, scheduling, and optional runtime support |
| Owns | Preservation of aligned pressures and unresolved boundaries |
| Does Not Own | Accepted async semantics or runtime contracts |
| Source / Provenance | Work items `001`, `005`, `006`, `012`, and `015`; foundational async, lifecycle, invocation completion, optional construction/transfer, execution-context, and process-wide collection pressure |

Current synchronous and ownership constraints are defined by
[Zax lifetimes and references](../../language/lifetimes-and-references.md) and
[Zax pointers and arenas](../../language/pointers-and-arenas.md):

- ordinary references do not safely cross thread boundaries;
- synchronous temporary extension does not cross suspension;
- unique ownership may cross only when instance affinity permits it;
- pointer-layer `atomic` protects shared lifetime accounting, not pointee
  operations;
- object and control-block arenas must outlive their dependencies; and
- final destruction must occur on a permitted thread or executor.

Future async work must preserve these constraints or explicitly revise them
through a separately reviewed language decision.

## Aligned pressure

Asynchronous programming, coroutines, and concurrency are foundational Zax
concerns. Computer I/O is inherently asynchronous even when exposed through
blocking interfaces.

Zax should provide first-class async semantics without imposing their runtime
cost on programs that do not use them.

## Costs and boundaries to preserve

Using async may require:

- coroutine state machines;
- executors or event loops;
- scheduling;
- allocation;
- synchronization;
- cancellation support;
- platform I/O integration; and
- runtime or library services.

These costs should remain visible and selectable where practical.

Async I/O, coroutines, concurrency, scheduling, and parallelism are related but
distinct. A future design must not collapse them merely because they interact.

## Execution-context and collection pressure

[Zax execution context](../../language/execution-context.md) establishes one
current `___` instance per thread execution path. Future async work must define:

- which context an async task captures;
- whether a task observes later replacement on its originating thread;
- how context changes are restored across suspension;
- what happens when a task resumes on another thread; and
- whether a child task inherits, copies, or explicitly receives a context.

Cycle collection is explicitly triggered for the complete process rather than
one arena. Future concurrency work must define how that pass coordinates
threads, suspended tasks, local and atomic ownership, graph mutation, roots,
destructors, and cancellation. An arena may publish global memory pressure but
does not independently collect an arena-local subgraph.

## Suspending lifecycle pressure

Current construction, replacement, and destruction are synchronous. A future
suspending constructor would expose partial state across a suspension:

```zax
+++ final async : ()() = {
  _.first.+++()
  await acquireResource()
  _.second.+++()
}
```

Before adopting such behavior, future async work must define:

- where the partial current instance is stored;
- which access paths may exist across suspension;
- cancellation after only some members are live;
- cleanup and allocator responsibility;
- which executor or thread resumes the lifecycle operation;
- whether callbacks or other tasks may observe the storage;
- when final and immutable guarantees activate; and
- whether replacement or destruction may also suspend.

An async factory can perform asynchronous work and then invoke synchronous
construction. That pattern remains available without making lifecycle operations
suspend.

## Ordinary-call suspension pressure

[Zax function invocation](../../language/function-invocation.md) defines call
completion only for synchronous calls. Future async work must decide:

- when an initiating async call is considered complete;
- where parameters, delayed defaults, argument temporaries, and result
  destinations live across suspension;
- what cancellation does after only some inputs or results are complete;
- whether callbacks may run after the initiating expression returns;
- how references into caller or producer temporaries remain valid;
- which executor or thread performs result mapping; and
- how coroutine, allocation, scheduling, and synchronization costs remain
  visible.

## Optional construction and transfer pressure

[Zax transfer stances](../../language/transfer-stances.md) and
[Zax optional values](../../language/optional-values.md) mark a wrapper present
only after its boxed construction completes. Async work must not publish or
observe presence while a suspending operation has established only part of the
boxed value.

Cancellation during optional construction or transfer must define:

- cleanup of partially established boxed members;
- whether accepted `move` has established the source's moved-from state;
- whether accepted protected `last` has marked the source terminal;
- when physical optional cleanup and wrapper absence occur across suspension;
- who owns resources between source release and destination adoption;
- when payload aliases and presence proofs become invalid;
- whether result mapping completed before another task can observe either
  wrapper.

These questions do not authorize suspending optional lifecycle operations.
Synchronous optional construction and transfer remain the current conceptual
boundary.

Legacy diagnostics also proposed requiring `deep` for async by-value inputs.
Future async work must review the actual need rather than retain that warning
mechanically:

- by-value `copy` may share backing under the type's `copy` contract;
- `deep` may establish independence at higher cost;
- `move` or `last` may be meaningful under an ownership-aware async contract;
- and references require a lifetime that survives suspension.

Representative legacy pressure, with illustrative unresolved async syntax:

```zax
sendLater final : ()(
  payload : Message deep
) promise = {
  // Is one explicit deep parameter sufficient?
}

sendEverythingLater final : ()(
  payload : Message
) deep promise = {
  // May a callable stance affect every input, result, and capture?
}
```

Current transfer design defines `deep` on the complete parameter or receiver but
does not accept automatic callable-wide propagation for async work. Future async
and generic design must decide the exact source contract rather than reviving
these examples by implication.

The eventual async contract must make the selected guarantee and cost explicit.

## Cross-thread preparation contract pressure

Transfer review exposed a candidate contract for producing a value whose
lifetime and ownership machinery may cross to another thread without depending
unsafely on the originating thread.

The final name and source form are unsettled. Illustrative future source:

```zax
worker.submit(source as cross thread)
// Illustrative future stance/contract name; not accepted syntax.
```

The intended guarantee is narrower than general thread safety:

- lifetime and ownership bookkeeping remains valid after crossing threads;
- deallocation and release do not depend incorrectly on the originating thread;
- thread-local or executor-bound resources are adapted, replaced, or rejected;
- and the destination may be moved to another thread under its declared
  contract.

It does **not** by itself guarantee:

- synchronized mutable access;
- absence of data races;
- lock-free behavior;
- immutable shared state;
- or that every operation may run from any thread.

### Several implementation strategies may satisfy it

Ordinary `copy` may already be sufficient for:

- inline values with no thread affinity;
- immutable global backing;
- operating-system handles valid from any thread;
- or atomic reference-counted shared ownership.

Atomic lifetime accounting only protects shared lifetime. It does not make
concurrent mutation of the referred value safe.

`deep` may establish sufficient independence when shared backing is the only
problem. It is not universally sufficient: a deep copy may still contain a
thread-local allocator relationship, GUI handle, event-loop registration,
executor-bound callback, or another thread-affine resource.

A specialized preparation may need to:

- replace non-atomic with atomic lifetime accounting;
- rehome allocation;
- detach or replace callbacks;
- freeze state;
- clone or reopen an external resource;
- or reject the transfer.

An accepted `last` source may make preparation cheaper by allowing ownership of
resources to move rather than be duplicated. `last` alone is not a cross-thread
guarantee: the transferred resource may still be thread-affine or share unsafe
lifetime state.

### Fallback is conditional

A possible preference is:

```text
exact cross-thread preparation
-> compatible deep implementation known to satisfy the contract
-> compatible copy implementation known to satisfy the contract
-> otherwise unavailable
```

This cannot be the unconditional `deep -> copy` ladder. The type must expose or
declare that the reused `deep` or `copy` implementation actually satisfies the
cross-thread lifetime contract. Future work must decide whether that relationship
uses an exact declaration, `= existing`, reflection, a generic constraint, a
type capability, or another contract mechanism.

### Stance or independent capability

The concept resembles transfer stance because source intent selects a consumer
and a containing implementation may propagate preparation into members.

It may instead be an independent destination guarantee because:

- it can be implemented through sharing, deep copy, move, or last transfer;
- a caller might need both terminal source permission and a cross-thread result;
- and it describes where the resulting value may safely live rather than only
  how the source is preserved.

Future work must test whether the concept is:

- a fifth transfer stance;
- a result or type capability;
- a generic constraint;
- a callable/capture contract;
- or a composition of existing transfer stance with another guarantee.

If it becomes a stance, it likely propagates through unstanced projections more
like `deep` than `move`: each member must become cross-thread transferable, but
the containing implementation may choose ordinary copy, deep copy, atomic
sharing, specialized preparation, or rejection for each resource.

### Representative structural pressure

```zax
ThreadMessage :: type {
  payload : Payload
  sharedCache : AtomicSharedCache
  callback : ThreadLocalCallback
}
```

Cross-thread preparation might:

- deep-copy `payload`;
- ordinarily copy `sharedCache` because its lifetime accounting is already
  cross-thread capable;
- replace or reject `callback`;
- and transfer another resource from an explicitly terminal source.

This example demonstrates why no one current stance is universally sufficient.

## Synchronous and asynchronous directive pressure

The root legacy compiler-directive page keeps the `synchronous` and
`asynchronous` directive sections as deferred input. They mark a `promise`,
`task`, or `channel` function as single-thread-only, or an ordinary function as
thread-aware, which changes whether pass-by-value arguments are expected to be
`deep`. Async work must decide whether these remain directives, which
[directive kind](../../language/compiler-directives.md#what-a-directive-may-promise)
they are, and what promise `synchronous` makes when the function is in fact
used from another thread.

## Activation and retirement

Activate this input before promoting async documentation, defining the
language/runtime lowering boundary, permitting lifecycle suspension, or defining
cross-thread value preparation. Consume its findings through that work and
retire or archive this placeholder afterward.
