# Raw input: purity and effects

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining a `pure` promise or broader effect tracking |
| Applies To | A declared promise that a function has no observable effects, and possible effect tracking beyond it |
| Owns | Preserved purity and effect-tracking ideas, representative source, activation pressure, and retirement |
| Does Not Own | Current execution policies, observable effects, or `auto` pre-computation ([compile-time code](../../language/compile-time-code.md#letting-a-function-decide-execution-policies)) |
| Source / Provenance | Work item `035` compile-time review, maintainer direction |

## Why this input exists

Current design already distinguishes work that has
[observable effects](../../language/compile-time-code.md#auto-stays-invisible)
from work that does not. The compiler may pre-compute effect-free `auto` work
invisibly, and a function author may declare `folding` so calls with constant
arguments run at compile time. Neither is a checked promise that a function is
free of effects.

## A `pure` keyword

The maintainer prefers a declaration keyword rather than a directive:

```zax
// Illustrative; not current design.
hashName final pure : (result : U64)(name : String readonly &) = {
}
```

A `pure` function would promise no observable effects, and the compiler would
check that promise against its body. Beyond allowing compile-time evaluation,
a checked promise would let the compiler:

- memoize results;
- remove duplicate calls with identical arguments; and
- reorder or drop calls whose results are unused.

Future work must decide:

- what counts as an effect for the promise, including reading globals, writing
  caches or locks, allocation, and panics;
- how `pure` relates to `folding`, which today may write globals and so is not
  itself a purity promise;
- whether `pure` is part of the visible prototype and how replaceable function
  slots and lambdas carry it; and
- source-stability rules when a library function stops being pure.

`folding` must remain meaningful without `pure`.

## Effect tracking beyond `pure`

Languages such as Haskell track effects in types: an effectful result has a
different type from a pure one, which makes effects composable and checkable,
at the cost of changing how every effectful call is written. `pure` is the
binary form of the same separation. A fuller effect system in that style is
recorded as a possible extension, not as Zax's intent.

## Activation and retirement

Activate this input when purity, effect checking, memoization, or effect-based
optimization is reviewed. Move accepted behavior into compile-time, declaration,
invocation, and diagnostics owners, then retire this file.
