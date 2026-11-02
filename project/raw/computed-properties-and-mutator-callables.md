# Raw input: computed properties and mutator callables

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work deciding computed-property, accessor, or callable-backed place behavior |
| Applies To | Legacy `mutator` evidence extracted before retirement of its root page |
| Owns | Getter/setter-like access pressure, polymorphic selection, hidden-call costs, replacement ambiguity, activation pressure, and retirement criteria |
| Does Not Own | Accepted declaration, invocation, property, assignment, operator, or callable-replacement syntax |
| Source / Provenance | Retired legacy mutator sections retained during work item `027` promotion |

## Reading posture

The source below is legacy illustrative evidence. The `mutator` keyword and its
shown declaration and assignment forms are not accepted Zax syntax. The
examples preserve behavioral pressure only.

## Computed read and write pressure

Legacy material allowed value access to select a zero-input callable whose
result matched the surrounding use, and assignment to select a callable that
accepted the assigned value:

```zax
// Legacy illustrative syntax; not accepted.
length mutator final : (output : Integer)() = {
  return output
}

length mutator final : ()(input : Integer) = {
  // Update backing state.
}

roomSize := 5 + length // Getter-like callable access.
length = 10            // Setter-like callable access.
```

The useful question is whether Zax needs computed getter/setter-like places at
all, and if so whether they are declarations, operators, explicit calls, or a
separate property abstraction. A future design must define evaluation order,
result and input routing, access eligibility, qualification, failure behavior,
and whether read-only or write-only forms exist.

## Polymorphic selection pressure

Legacy `mutator` declarations could form a polymorphic family:

```zax
// Legacy illustrative syntax; not accepted.
person mutator final : ()(input : Integer) = {
}

person mutator final : ()(input : String) = {
}

person = calculateHistoricalAge()
person = "Socrates Johnson"
```

Future work must decide whether ordinary callable candidate comparison applies,
how an expected result selects a getter-like form, how an assigned source
selects a setter-like form, and when nondominated candidates are ambiguous.
Source, declaration, import, or discovery order must not become an implicit
tie-breaker.

## Hidden call and cost pressure

Property-looking read or assignment source may hide:

- candidate lookup and indirect invocation;
- arbitrary computation, allocation, synchronization, or panic;
- repeated work when an apparently simple value is read several times;
- mutation of backing state or unrelated observable state; and
- receiver, capture, and lifetime costs.

Any accepted design must make those costs visible to programmers and tools.
Optimization must not assume field-like purity merely because source omits call
parentheses.

## Replaceable implementation ambiguity

Legacy material also allowed assignment to a non-final `mutator` to replace its
callable implementation when the assigned value matched the callable prototype.
That makes the same assignment shape potentially mean either:

1. invoke a setter-like callable with a callable-valued argument; or
2. replace the getter/setter implementation itself.

The legacy proposal preferred implementation replacement for an exact callable
prototype. That priority is not accepted. Future work must avoid or explicitly
resolve this ambiguity, preserve final/varying storage rules, and distinguish
updating a computed property from updating the callable that implements it.

## Activation and retirement

Activate this input when computed properties, accessor declarations,
property-like places, hidden invocation, callable-valued setters, or replaceable
accessor implementations are reviewed.

Move accepted behavior into declaration, invocation, assignment/place,
qualifier, callable, operator, diagnostics, and cost owners as applicable.
Retire this file after the preserved pressures are accepted, rejected, or moved
to narrower live destinations.
