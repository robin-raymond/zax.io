
# compiler-directives.md

These are the maintainer's notes on compiler directives that remain useful after
the compile-time, directive, and generics reviews. Every other note was consumed
by those reviews. The note below carries direction for source generation, and
takes priority over legacy directive material where they differ.

## Generators

For the generation stuff, I think that should be separated out. I do want generators, and they do need to execute at compile time but I think I'd like to do that separate so we should leave that part in the original document for now.
