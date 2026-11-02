# Raw input: remaining callable-adjacent pressure

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining optional combinators, restricted capture scopes, callable generics/reflection, or advanced call/mixfix integration |
| Applies To | Callable-adjacent material deliberately not resolved by current lambda and composition design |
| Owns | Optional callable construction/combinator pressure; restricted `scope` capture evidence; advanced `operator call`/mixfix pressure; generic and reflection questions; activation and retirement |
| Does Not Own | Current lambda, capture, callable-storage, composition, or chaining behavior |
| Source / Provenance | Retired legacy function-composition material, optional review, operator review, and work item `027` disposition |

## Current behavior moved

Current lambda expressions, capture construction, callable storage and receiver
lifetime, partial application, positional and reshape composition, immediate
chaining, reset, weak invocation, and costs are now owned by
[Zax lambdas and callable composition](../../language/lambdas-and-callable-composition.md).

This raw file must not be used to recover competing `>>`, `|>`, capture, or
receiver-reattachment behavior.

## Restricted captured scope

Legacy input proposed a non-callable restricted scope:

```zax
// Legacy illustrative syntax.
scope [ myInput, &myOutput ] {
  myOutput = transform(myInput)
}
```

Future flow/scope work must decide whether this remains independently useful or
is better expressed by ordinary declarations, a lambda, or a direct call. If it
remains, names absent from the list are unavailable inside the scope, ordinary
capture copies, and explicit reference capture borrows one fixed place.

## Optional callable construction

An existing optional may construct a present callable through an ordinary
construction packet:

```zax
callback : MyCallback?
callback = [{ doSomethingFunc }]
```

Future optional/callable integration may permit an inline lambda as the one
packet input. Packet braces and lambda body braces must remain distinct, and
`[{}]` remains the zero-entry construction packet.

## Optional combinators

Optional values may benefit from operations such as:

```zax
fold(optionalValue, onPresent, onAbsent)
```

Future work must define:

- only the selected callback executes;
- how a present callback receives boxed qualifications and lifetime proof;
- how outer transfer stance reaches the callback;
- result convergence between present and absent callbacks;
- capture, allocation, temporary, and indirect-call costs;
- async callback behavior; and
- protected operation versus library ownership.

This pressure does not establish a general monad abstraction. A reusable model
would require concrete evidence across several value families.

## Call-form mixfix pressure

A custom callable may use a `call N` mixfix component. Future work must
integrate labels, defaults, variadics, result forwarding, and generated callable
types without creating an intermediate call result for a consumed component.

## Generic and reflection pressure

Future generic work must decide:

- generic lambda parameter and constraint syntax;
- specialization identity and body minting;
- type erasure and finite runtime callable surfaces; and
- interaction with exact callable prototypes.

Future reflection must distinguish:

- anonymous generated receiver identity;
- capture declarations and source presentation;
- fixed/varying and bound/unbound static storage capacity;
- the installed callable binding kind;
- visible prototype versus minted implementation;
- receiver ownership/observation mode;
- source availability and reset state; and
- generated composition stages and reshape mapping.

No exact reflection source form is accepted here.

## Activation and retirement

Activate this input for restricted scopes, optional callback construction or
combinators, generic lambdas, callable reflection, or advanced call/mixfix
integration.

Move accepted behavior into its domain owners. Retire this file when every
remaining concern has been promoted, rejected, or moved to a narrower indexed
input.
