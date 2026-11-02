# [Zax Programming Language](index.md)

## Flow Control

Current synchronous flow control is owned by
[Zax core flow control](language/core-flow-control.md): exact-`Boolean`
conditions, `if`/`else`, header initialization and posts, the conditional
expression, `while`, `until`, `redo while`, `redo until`, `forever`, explicit
`scope`, flow labels, and `break`/`continue`/`next`/`goto`/`return`.

Current `switch`, `case`, and `default` behavior is owned by
[Zax switch, case, and default](language/switch.md). Its ordered
first-match model, case operators, clause posts, transfers, optional proof, enum
coverage, and diagnostics supersede the former switch material on this page.

Current `using` resource-list, structural-disposal, ordering, and exit behavior
is owned by
[Zax `using`](language/using.md).

The sections below are the **legacy remainder** of this page. They preserve prior
design evidence for value polymorphism using `if`. This evidence remains
non-authoritative until its own focused review.

### Value polymorphism using `if`

An `if` statement can also be used in a function declaration to indicate that a function supports value polymorphism. Which function to call is based on a pre-condition check for a given `if` statement. The compiler will execute each test based on the order of appearance in code if no specific order has a bias. If no match is found (and if present) then a undecorated version of a function will be executed. A compiler may decide to reorder tests if the reordering will have no net resulting impact on the code flow. Care should be taken to not have overlapping pre-conditions if code order cannot be preserved or guaranteed. The `[<likely>]` and `[<unlikely>]` compiler directives can be used as a hint to a compiler which execution path is more likely to be followed (thus tests can be reordered appropriately).

If some value polymorphic functions are declared using an `if` statement then a single polymorphic version function using the same types can be declared as a catch-all if none of the other conditions succeed (i.e. the logical equivalent of a `switch` `default` statement). If no function was found a panic may be issued.

Only functions marked as `final` support value polymorphism. A conditional check on a function cannot be replaced and any assignment of a changeable functions would be ambiguous to which value polymorphic version should be replaced. However, a function without any value polymorphism `if` condition can be `varying` allowing the function to be reassigned to a new function implementation that will assume to replace only a default non-conditional version (i.e. a version that does not contain value polymorphism).

````zax
random final : (value : Integer)() = {
    // ... return a positive or negative integer
}

func final : ()(value : Integer) if { return value > 0 } = {
    // ...
}

func final : ()(value : Integer) = {
    // ...
}

forever {
    // each time the function is called a different function may be invoked
    func(random())
}
````

Another example computing factorial:

````zax
assert final : ()() = {
    // ...
}

factorial final : (r : Integer)(n : Integer) if { return n > 1 } = {
    return n * factorial(n - 1)
}

factorial final : (r : Integer)(n : Integer) = {
    return 1
}

assert(120 == factorial(5))
````

An example of a children's game of FizzBuzz using value polymorphism:

````zax
print final : ()(...) = {
    // ...
}

toString final : (result : String)(value : Integer) = {
    // ...
}

next final : (s: String)(i : Integer) if [<unlikely>] { return i % 15 == 0 } = {
    return "FizzBuzz"
}

next final : (s: String)(i : Integer) if [<likely>] { return i % 3 == 0 } = {
    return "FizzBuzz"
}

next final : (s: String)(i : Integer) if { return i % 5 == 0 } = {
    return "Buzz"
}

// next is not marked as `final` and can be replaced with an alternative
// implementation
next : (s: String)(i : Integer) [<likely>] = {
    return toString(i)
}

// displays: 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, ... 14, FizzBuz, ...
while i := 1 ;; i < 100 ;; ++i {
    print(next())
}
````

#### Nothing-instance disposition

The former `+++(: Nothing)` value-polymorphism branch is superseded and
removed.

Inside a `once bound` function, protected `?_` distinguishes a receiverless
type call from an instance-qualified call. On the receiverless route `_`
identifies the type's Nothing instance; on the instance route it identifies the
evaluated receiver. This is invocation behavior, not `if`-based overload
selection. See
[Zax function invocation](language/function-invocation.md#type-and-instance-calls-to-once-functions)
and [Zax Nothing instances](language/nothing-instances.md).
