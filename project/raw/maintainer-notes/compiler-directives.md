
# compiler-directives.md

This file is the maintainer notes on the compiler directives concept. This represents my updating thinking on the existing directives. The existing directives are not canon and there is no legacy to maintain. We need to make sure the directives are well suited for their job. Under each heading related to a compiler directive I'll put my updates thoughts on that particular directive. If I put `None.` then I have no updated thoughts. You should review with your own perspectives, opinions, and concerns on each directive.

The work related to the compiler directives is large so it might be required that the various aspects of this work package be done in batches to not fill up the 1M context during the later stages. The maintainer will advise when the context is getting too full as self-probing the context size has proven unreliable.

## Using `x-` prefix

I do like this, and I think we should recommend compilers add their own naming prefix to distinguish their directives unless multiple vendors are collaborating (if there's every greater than myself).

## `source` directive

None.

## `asset` directive

None. As Zax has ordered compilation it's not unreasonable that compilation must have happened prior to the generation statement.

## `panic` directive

THe previous panic applies to a code range. I think this is likely the right approach. Putting a scope around areas seems too narrow as it's likely the entire area is to be disabled.

The biggest issue is related to import. If we don't carry the panic directive into the import then we need a way to send compile time values into the import so that the import can enable/disable panics accordingly. I'm not sure that's the best policy. Instead, I think it might be better to let the import carry the panic directive and allow the import to change their code entry to whatever panic states they want if they don't like the defaults carried from the importer. What likely is needed though is a way to reset to the defaults because new panics might be added and it would be difficult to retrofit all possible panics into existing code as the language evolves. So if the imported module wants "defaults" it should be possible that is specified.

## panic function

There's very little a panic can do to fix the situation. Thus I think any panic function is only to be able to install custom state captures at most. The panic function once returning from the call will cause a graceful crash. One concern, what if the pointer to the panic function is wrong because corruption has happened? I think this might be acceptable if we have an internal pre-panic panic call capture that happens regardless of the invoked panic.

Disabling panic is a promise by the programmer that this panic can never happen. Thus the check is removed, so any failure for the programmer to live up to their contract will cause undefined behavior (as the check is removed).


## Legacy: Panic registry and meanings

It's unclear if these still apply or are already replaced by new definitions. We might need to keep some stubbed for areas we've not yet covered yet.

## `deprecate` directive

My issue with the way it's defined is the ability to apply to declarations that follow. Inside a `type`, or other definition, it might not be so bad because at the end the enclosure, but at the global scope it doesn't make sense, especially without a `push` and `pop` version.

The other concern is when it should be attached to a particular entry vs being attached to a particular entry. I think that can be solved by placement. If placed directly above an entry it can be attached. There's also the `always` `never` which can be used with placement to indicate intent. Anything mismatching from placement and attachment should cause an intent error.

## `inline` function directives

I think this is fine, after the `final` declaration for a function. I'm skeptical of `descope`. I think the intent is to be able to declare a small snippet of code effectively where-ever a function is declared / used. But it will break in so many scenarios so I think that likely should be removed.

## `compile` directive

I like the concept but I have two main concerns:
- the directive name `compile` I'm not sure is the best naming because of similar directive names
- does the directive become part of the prototype for non `final` functions? I think "yes". I think a replacement body must also adhere to the contract. We have precedence related to return defaulting being part of the contract and I think this is similar?

Is `final` required for all values? `readonly` wouldn't need it, I think, but `immutable` I think needs to be paired with `final` so the replacement constructor can't be called on the value.

## `compilation` directive

I'm not sure this should exist. The use case shows detecting basic type sizing, but we already have namespacing to check, at least for basic types. Outside of this use case I don't understand what this directive would do, and I see questions arising how to handle it. So I think this directive should be removed.

## `compiles` directive

The idea is good, there's a bit of a problem as defined. The issue is that the compiles directive needs to resolve to an expression of `true` or `false`, but it needs to function as a multiple statement command in a way that's a full scope. Whatever is inside the code cannot convert into actual code statements. It must pass ever other test, except for actually converting into real code.

The way I envision it being legal to compile in place is as if were were compiling a hidden lambda that returns true or false and is immediately executed.

For example
```
myBoolean : Boolean = [[]](result : Boolean)() {
  ... code to check if compiling ...
}() // call immediately in-place
```

It should behave as if it's a lambda that default captures any variable in context, except none will ever resolve their value. I don't mean it should literally convert into a lambda. I'm giving an analogy of how it could work because otherwise things like this make no sense:
```
value := unspecifiedFunctionResult()

if [<compiles>] { ++value; --value; value *= 3 } {
  // ... how else would multiple statements in the code make sense contextually where an expression belongs? ...
}
```

Also we should consider:
```
result := compiles{
  someStatement()
  anotherThing = 4
}
```

Maybe is shouldn't be a directive. The issue is that we'd want to be able to error it instead of it resolving as true or false. That could be `compiles<>{ ... }` and `compiles<error-on-failure>{ ... }` or it could be an entirely different statement.


## The requires directive

This is just like the compiles directive, except it does even more-so resemble a lambda that is default executed which returns a `Boolean` value. Thus is must both compile and return true after compilation at runtime execution. The issue will be that the values can't be "captured" if they are runtime values as requires must evaluate at compile time. Further if values were captured, it could only be done by value because it can't leave side effects. So this to me suggests that it cannot actually capture any real runtime value. At most it could derive new values based on surrounding types. As there are ways in Zax to use the "type of" something in new types, that aspect can safely be obtains; compile time values could be auto-captured by value as if it were a lambda but even that doesn't make sense because compile time could just use the surround variables without needing to use the `requires` clause at all. The requires statement block always requires a `return` result of `true` or `false`, and the result name is unnamed thus can't be specified, like `result = true`.

Same concern with `compiles{...}` should it be a version of that, like `requires{ ... }` but I'm not sure, and what should we call these?

There's also an C++ style SFINAE demonstrated that is being demonstrated with `requires`, but it's not really related to requires. The idea is that a `final` function could be declared or not declared depending if the `true` or `false` being present as part of the declaration. For a polymorphic function, definitions could be compiled out by this method. I'm not sure SFINAE this is the right approach for doing that.

## `concept` directive

I would suggest we leave the `concept` directive alone and not examine it until we've covered generics. We can factor we'll want some kind of concept like this later but not the specifics of how yet. This should remain as work for generics.

## `execute` directive

I think my concern with this directive is the mixing of a bunch of different options.

First, `host` and `target` should have their own independent execution criteria:
- always run at runtime
- execute at compile time if all the parameters passed are compile time
- execute at compile time, and error if it's not possible

For the generation stuff, I think that should be separated out. I do want generators, and they do need to execute at compile time but I think I'd like to do that separate so we should leave that part in the original document for now.


## `resolve` directive

I think there's a need for this but I do want your opinion on this feature and how it can/should work.

## `align` directive

We do need some way to specify the alignment of values.

## `reserve` directive

I think this is needed.

## `void` directive

This is another low level structural thing.

## `likely` / `unlikely` directive

Aside from the placement details, I think this adds value to indicate path optimization for the compiler so it doesn't have to guess about likely paths when known.

## `always` and `never` directives

Aside from the placement details, I think this can help indicate paths that must never happen, yet they are still checked paths.

## `export` directive

Relate to import, the export is needed to export values for import.

## Literal directives

I'd like a review on these to see if they make sense.

## Override literal directives

This is mostly about other languages being able to generate Zax code but report the original code as the origin and compilation.

## `lock-free` directive

This is helpful to avoid some known issues around singleton locking when it's actually not needed.

## `synchronous` directive

I think we should leave this directive for when async programming is done.

## `asynchronous` directive

I think we should leave this directive for when async programming is done.

## `variables` and `types` qualifier defaults directive

I'm highly skeptical of overriding the defaults. It could cause copy and pasted code into other areas to behave dramatically differently.

## `functions` default directives

I'm not sure about this feature.

## `abi` directive

I think we have to leave this for a later time.

## `tab-stop` directive

Given it's an error to have tab stops, I think this isn't needed.

# other concerns

We have the `intent<...>{ ... }`, `unsafe<...>{ ... }` and `bare{ ... }` mechanism for what look like compiler directives, and they kind of are... I don't know if we have a way to normalize these as a collective. Not all compiler directives have properties like `intent<...>` and not all of them have a scope-like application area like these do, e.g. `bare{...}`. And if we bring all the directives as these kinds of keyword things then that expands the keywording to a large domain of possible keywords. And I don't like the idea of doing `[<intent,property>{ ...}>]` nor `[<bare>]{ ... }>]`. Thus I don't know how to reconcile the differences and why one is done this keyword way and the others are a directive. It's a gut feeling only instead of a rule. I also don't know which (if any) of the directives should instead become keyword-ish instead. Maybe those that involve scope-ish definitions? That would expand the combinations a lot though. And the `-` usage in directive names doesn't work well for keywording.


