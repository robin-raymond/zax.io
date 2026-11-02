# Raw input: generalized pattern matching

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work reviewing patterns, destructuring, scoped projection, guards, or selected payload binding |
| Applies To | Selection and scoped access shapes that cannot be expressed as current Boolean case tests, ordinary proven access, or type-member composition |
| Owns | Preservation of generalized-pattern pressure, recursive destructuring, scoped projection, nested selected-payload binding, guarded patterns, binding convergence, and interaction with direct body entry |
| Does Not Own | Current runtime `switch` behavior, accepted variant syntax, or current optional access |
| Source / Provenance | Former raw selection input consumed by runtime-selection review; work item `018`; raw variants/unions and optional matching pressure |

## Reading posture

Current [switch, case, and default](../../language/switch.md)
defines `case` alternatives as Boolean operations over one retained selector.
Those alternatives introduce no payload bindings and are not generalized
patterns.

This file preserves only the pattern-like remainder. It accepts no syntax or
behavior and is not required for ordinary selection reading.

## Why a distinct future form is needed

Current cases can:

- compare through implicit equality;
- apply direct binary or post-unary tests;
- apply an operandless pre-unary test;
- establish recognized path facts such as optional presence; and
- use ordinary nested selection and postfix access.

Ordinary Boolean cases cannot:

- destructure a selected value;
- declare names in a case header;
- recursively match nested shapes;
- attach a guard that resumes later patterns when false; or
- guarantee one binding shape across structurally different alternatives.

Variant-specific cases are the narrow exception: they route and bind one
top-level active payload under the current variant owner. They still do not
destructure that payload or provide general patterns.

Future pattern syntax must remain visibly distinct from current Boolean case
tests. It must not silently reinterpret existing source such as:

```zax
case expectedValue
case < upperBound
case ?
```

## Optional payload-binding pressure

Current optional selection crosses one layer at a time and uses ordinary proven
access:

```zax
switch nested {
  case ? {
    switch nested. {
      case ?
        use(nested..)
      default
        handleInnerAbsent()
    }
  }
  default
    handleOuterAbsent()
}
```

A future pattern feature may seek a flatter payload-binding form. The following
is illustrative and unaccepted:

```zax
switch nested {
  case present(present(value : MyValue &)) // illustrative future pattern
    use(value)
  case present(absent) // illustrative future pattern
    handleInnerAbsent()
  case absent // illustrative future pattern
    handleOuterAbsent()
}
```

Any accepted form must preserve:

- outer absence, outer-present/inner-absent, and both-present as three distinct
  states;
- one-layer-at-a-time presence proof;
- the exact boxed qualifications and lifetime;
- invalidation when reset, packet construction, wrapper replacement, or another
  operation ends that boxed lifetime; and
- no treatment of a construction packet as an anonymous value.

## Variant routing is current; destructuring remains future

Current [Zax variants](../../language/variants.md) and
[switch](../../language/switch.md#variant-alternative-selection) now define
finite named-alternative routing, reference-shaped payload binding,
polymorphic `bind`, absence/presence cases, coverage, and direct-entry
restrictions:

```zax
switch event {
  case message
    handle(message)
  case closed
    handleClosed(closed)
  case !
    handleAbsent()
}
```

That accepted form is not generalized pattern matching. This file retains only
pressure to destructure a selected payload recursively, bind nested parts, add
guards, or converge more elaborate binding shapes.

Unmanaged unions have no active lens or selection state and therefore provide
no union pattern route. Future pattern syntax cannot manufacture an
active-member model for their overlapping representation.

## Guards and resumption

A pattern guard would need to decide whether guard failure resumes with later
patterns and which facts survive:

```zax
switch event {
  case MessageEvent(message : Message &) if acceptable(message)
    handle(message)
  default
    reject(event)
}
```

This source is illustrative and unaccepted. Future work must establish:

- whether the guard is exactly `Boolean`;
- when payload bindings become live;
- whether a false guard destroys them before later testing;
- which effects and evaluation order are observable;
- how guards affect overlap and exhaustiveness; and
- whether direct entry can target a guarded body.

## Binding agreement and direct entry

Comma alternatives currently share one body because they introduce no bindings.
Pattern alternatives that bind values would need one usable body environment:

```zax
case Left(value : MyValue &), Right(value : MyValue &) // illustrative
  use(value)
```

Future work must define whether every alternative must establish the same names,
types, qualifications, and lifetimes or whether another explicit convergence
form is required.

Current `goto case_label:` enters a body without running its tests. A future
pattern-bound body therefore cannot be an ordinary direct-entry target unless
every required binding and proof already exists on that incoming path. Possible
designs include rejecting such a target, requiring a separate unbound body
entry, or defining another explicit mechanism. This file chooses none.

## Value-producing selection boundary

A future selection expression may interact with patterns but is independently
motivated. It must define direct result construction, exact path convergence,
exhaustiveness, and transfer restrictions. The current statement switch does
not imply one result slot or a value-bearing `break`.

The current runtime-selection owner retains that boundary. Move it here only if
future pattern and value-selection work become one cohesive feature.

## Scoped projection pressure

Current [`own`](../../language/composition.md) applies only to stored type
members. It does not publish names from a local declaration or introduce
temporary member lookup into a flow scope.

Future destructuring or scoped-projection work may revisit the usability goal:
make selected parts of one local value conveniently available for one bounded
region without changing the containing type. It must not reuse type-member
`own` in a way that:

- injects functions or operators into lexical lookup;
- hides whether a name is a local binding or a projected member;
- turns data projection into receiver or operator discovery;
- changes storage, lifetime, or transfer stance;
- silently resolves collisions by source order; or
- reintroduces the retired `using value own` spelling.

This is pressure for a distinct scoped or pattern-binding feature, not deferred
permission to extend current composition syntax.

## Activation and retirement

Activate this input when a concrete use case requires:

- destructuring rather than Boolean testing;
- nested binding inside an already selected payload;
- guarded patterns;
- recursive shape matching;
- bounded local member projection or destructuring; or
- pattern-aware value-producing selection.

Future work must begin with current runtime selection, optional proof, variant
lifetime, construction, qualifier, and direct-entry constraints. Move accepted
behavior into a dedicated pattern owner and applicable domain owners, then retire
this file.
