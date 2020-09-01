
# [Zax Programming Language](index.md)

## FAQ


### When was the idea of Zax inspired?

The concept of writing the Zax language has been around for a long while. With Robin's many years of software development, some ideas of best practices and desired features in a language formed over time as well as a syntax that enables those concepts into reality. The language is designed for programmers who desire to remain closer to hardware while having access to higher level language convenience for those with the skill levels string enough to understand the implications of their coding decisions.


### Can Zax be used by beginners?

Of course. Many programmers start out with lower level languages. Some concepts are easier to understand if the implications of what's actually happening on the hardware is understood. Be aware that Zax will not prevent the programmer from writing unsafe code. In other words, programmers should not write code in Zax without the programmer assuming full responsibility for mistakes the language will knowingly and willingly allow. If safety is important, other languages with stronger guarantees should be used.

### Why is the language strongly typed?

Since the language is designed to be compiled without the need of a runtime support component, the compiler needs to know exact type sizing to generate platform specific CPU instructions. 


### Why is the language compiled?

Speed and efficiency is the primary reason. See [Advantages vs Disadvantages](https://en.wikipedia.org/wiki/Compiled_language#Advantages_v._disadvantages) for more reasoning. At the moment, [Moore's law](https://en.wikipedia.org/wiki/Moore%27s_law) seems to be reaching a plateau for single threaded capacity. Lots of tricks, and parallel processing techniques can be used to increase overall CPU capacity but ultimately resources running in a single thread should be as efficient as possible. Designed correctly, a compiled language can be fast to iterate between code runs too.


### Why is the language considered data oriented?

The language is designed around data types and does not attempt to enforce traditional data hiding and function virtualization encouraged by traditional Object Oriented designs. The assumption of Zax is that the programmer should understand how data and code become rendered on target systems and can better take advantage of this knowledge accompanied with like minded individuals who share an eco system that encourages data oriented designs.


### Why doesn't the language enforce memory safety?

Languages like [Rust](https://en.wikipedia.org/wiki/Rust_(programming_language)) have extensive compilation introspection that enforce safety and may (or may not) include an `unsafe` style keyword to bypass these restrictions. In fact, this type of compiler might be considered vital for secure code authoring. The downside is that these enforced rules can get opinionated in how code should be designed and executed. While Zax does have its opinions, Zax favors freedom to organize data and code flow over safety. The programmer can decide their own tradeoff between safety and being walled into opinionated paradigms. Hopefully, the language features and tools are powerful enough that potential drawbacks of non-enforced safety are mostly overcome.


### C++ or _insert language here_ is better in every way?

Probably. That's not the point. Languages can get stagnant overtime and can suffer under their own weight of legacy. This does not mean they are bad but they might not be able to experiment in ways new languages which have a clean slate that might lead to more effective and efficient programming tooling.


### Why does the language clearly resemble X language?

A similarity might be by design, osmosis, or by rationalizing to the same conclusion. Computer languages have been around for a long time. All knowledge is built upon the greats of old.


### Zax will get lost amongst all the other languages?

Every popular language started as a thought in someone's mind at some point. This language attempts to be different enough to hopefully get a small snowball running downhill effect going. However, even as an experiment the knowledge gained can be useful and insightful.


### What is special about Zax?

Zax was inspired by [Jai](https://en.wikipedia.org/?title=JAI_(programming_language)) which is heavily focused on solving the gaming industry's woes with C++ and other languages. Some of the important concepts include the idea the language self includes integrated build processes with full code execution and reflection at compile time. However, Zax is designed to contain many of the interesting concepts but targeted at a more general purpose language usage. Some ideas differ in design and implementation, or perhaps they don't given not everything about the language is documented for outside usage. Jai being targeted at a specific industry might enjoy more success but experimentation and knowledge sharing is a good thing. The goals for Zax are not contingent on its popularity.


### Why are variables declared before types unlike C, C++, or Java?

Not all languages place the type before the variable name. [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)#Data_types) has history longer than [C](https://en.wikipedia.org/wiki/C_(programming_language)) and declared the variable name before the type. Other modern languages such has [Go](https://en.wikipedia.org/wiki/Go_(programming_language)) have made the same decision for their own [reasons](https://golang.org/doc/faq#declarations_backwards).

The beauty of the syntax is subjective, but the reasons distills down to:
* emphasis on the variable name since the grammar is designed to be read left to right
* variable declaration does not require a placeholder keyword to indicate a variable declaration

````c++
// The variable type is declared first defining the type of the variable
float weight = 0.0;

// Even when the variable type can be deduced, a type placeholder is still
// required.
auto weight = 0.0;
````

````zax
// As the name carries the meaning of the type, the variable name is placed
// first and the type after for emphasis on the variable name when reading
// left to right.
weight : Float = 0.0

// Where the type can be deduced, the type can be entirely eliminated from
// the declaration entirely.
weight := 0.0
````

### Why aren't code examples syntax highlighted?

None of the keyword show up as highlighted:

````zax
:: import Module.System.Types

example :: type MyExample {
    value : Integer
}
````

This site is currently hosted with GitHub which does not appear to support syntax highlighting of languages not officially recognized by GitHub (at least without complex external build processes).


### If this language is data orientated, why does the language support some Object Oriented features like constructors/destructors?

Data Oriented design puts the focus around the data whereas Object Orientated attempts to hide the data inside encapsulated objects that have actors and actions that apply to the object. While data design is promoted strongly in video game design, the principles are sound for generalized programming too. Concepts like constructors and destructors or functions on types can be used in a Data Oriented mode or an Object Orientated model. The difference is the language is not attempting to provide object abstraction models and instead put the focus entirely on the data and its organization. Other concepts typically associated with Object Orientated design are not unique to that design principle. For example, polymorphism can equally apply to a procedural language as it does an Object Orientated language despite its strong association with the latter.


### Isn't Zax just syntax sugar?

That could be said for every language. Isn't C just syntax sugar for assembly? Isn't C++ syntax sugar for C (plus a few features)? Zax attempts to marry some of the best concepts from multiple languages. Zax is not aiming to be the most powerful language but it does aim to be a pleasing language to work within for a compile time language.


### Are virtual functions supported?

No. However, types can contain pointers to functions. Function pointers can be replaced with new code easily. Functions can be overridden in containment. Functions can be replaced at runtime. Values can even be captured during function replacement.

With existing languages, typically virtual-tables are generated as a pointer to a lookup-table containing an array of pointers to functions. This lookup process is expensive during runtime for repeated calling. The pseudo logic to execute a virtual table function is `((*(type.virtualTablePtr))[n])->function()`. This methodology involves a lot of indirection at the savings of being able to combine the lookup table for all instances of the same class/struct into the same table.

In Zax, the pseudo logic for calling replaceable function is `(type.func)->function()` for functions that can be overridden. The trade off is that more memory is used per instance of a type for each overridden function at the benefit of less calling overhead and less indirection and greater override flexibility. Pus, an entire meta-type system exists allowing strongly typed functions without the need of function overrides.

Virtual function have even more unseen overhead than the additional CPU lookup instructions. Lookups can be made even more expensive by virtue of data non-locality and CPU cache misses. By accessing a pointer to a table somewhere else in memory a CPU cache miss can happen which would be less likely if the pointer was directly inside the structure.


### Shouldn't concurrency features just be a library?

While it's possible to program all of the concurrency features within in a library, without language support, some of the concurrency features would be extremely ugly to develop or would require complex code introspection. For example, the compiler rewrites task functions into suspend/resume blocks of code. Other features (such a `strong` pointers or `deep` copy) are doable but they can make code ugly with complex templates and operator overriding instead of simple type qualifiers.
