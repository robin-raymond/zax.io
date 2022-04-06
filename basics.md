
# [Zax Programming Language](index.md)

## Basics

### Basics of parsing

````zax
//---------------------------------------------------------------------------//
// C++ style inline comments

/*
   C style multiline comments
*/

// The semi-colon `;` is not necessary between statements, and has a different
// role to play in the Zax language and does not mirror the "C" language. The
// semi-colon `;` operator is used two combine one or more separate statement
// as if it were a single statement.
funcA()
funcB()

// single line with multiple statements treated as a single statement
funcA(); funcB()

// a scope of statements is treated as a single statement for flow control
// purposes
{
    funcA()
    funcB()
}

// a scope combines multiple statements as if it were a single statement
if true {
    funcA()
    funcB()
}

// a semi-colon `;` operator combines multiple statements as if it were a single
// statement
if true
    funcA();  // this line is part of the `if` statement as normal
    funcB()   // this line is also part of the `if` statement as it is combined

// if code needs to span multiple lines then the continuation '\' operator can
// be used
value := 2 + func() \    // continuation of statement to multiple lines
         * 3 \
         / 2

// keywords are all lowercase and keywords are reserved only where they may be
// legally used
if condition() {
} else {
}

// Additional nested multiline comments are supported
/**
  Nested multi
/**
  multi line comments
**/
  comments
**/

/// <summary>
/// Triple slashes `///` are treated as a documentation comments and if XML
/// is detected at the start of the documentation comment then the comment is
/// assumed to be an XML style documentation comment.
/// </summary>
someType :: type SomeType {}

/// Automatic summary documentation about `func`.
func final : (
    output : Integer   /// Automatic summary comment about `output`
)(
    input : Integer    /// Automatic summary comment about `input`
) = {
    // normal code comment
    // ...
}

///***

<summary>
Triple slash with triple stars `///` `***` with closing triple start with triple
slash `***` `///` comments are treated as documentation comments without the
need to place `///` on every single line. In fact, putting `///` or `*` or any
other symbol inside comment would be treated as part of the documentation. If
XML is detected at the start of the comment then XML style documentation is
presumed. Otherwise the comment is assumed to be a large-style summary comment
for a given context.
</summary>

***///
````


### Keywords

````zax
alias
await
break
case
catch
channel
continue
collect
constant
copy
deep
default
defer
discard
each
else
except
false
forever
from
handle
hint
if
in
is
immutable
import
inconstant
keyword
last
lazy
lease
managed
move
mutable
mutator
once
operator
override
own
private
promise
raw
redo
return
scope
shallow
suspend
switch
task
true
type
union
unique
unsafe
until
using
varies
weak
while
yield
````


#### Keyword disambiguation

Keywords are reserved only within the context of where the keyword is allowed and legal. To disambiguate keywords from variables, variables can be postfixed with an underscore (`_`) but otherwise the underscore postfix (`_`) should never be used. Usage of keywords as variables is discouraged and usage of postfix underscores are also discouraged.

````zax
// legal name because `constant` is a keyword in some contexts
constant_ : Type constant

// foobar_ is not a legal name because it contains an _ postfix on a name
// that is not a keyword
foobar_ : Type constant
````


### Operators

#### Overloadable
````
+               // pre-unary plus operator
-               // pre-unary minus operator
+               // binary plus operator
-               // binary minus operator
++              // (pre or post) unary increment operator
--              // (pre and post) unary decrement operator
*               // binary multiply operator
/               // binary divide operator
%               // binary modulus divide operator
=               // binary assign operator
^               // binary bitwise xor operator
&               // binary bitwise and operator
|               // binary bitwise or operator
<<              // binary bitwise left shift operator
>>              // binary bitwise right shift operator
<<<             // binary bitwise left rotate operator
>>>             // binary bitwise right rotate operator
~               // pre-unary bitwise one's compliment operator
~|              // pre-unary bitwise parity operator
~&              // binary bitwise clear operator
!               // pre-unary logical not operator
&&              // binary logical and operator
||              // binary logical or operator
^^              // binary logical exclusive or operator
+=              // binary add and assign operator
-=              // binary subtract and assign operator
*=              // binary multiply and assign operator
/=              // binary divide and assign operator
%=              // binary modulus divide and assign operator
==              // binary equal operator
!=              // binary not equal operator
<=>             // binary three-way comparison operator
<<>>            // binary two-way swap
<               // binary less than operator
>               // binary greater than operator
<=              // binary less than operator
>=              // binary greater than operator
~=              // binary bitwise one's compliment operator
^=              // binary bitwise xor and assign operator
|=              // binary bitwise or and assign operator
~|=             // binary bitwise parity operator
~&=             // binary bitwise clear and assign operator
<<=             // binary bitwise left shift and assign operator
>>=             // binary bitwise right shift and assign operator
<<<=            // binary bitwise left rotate and assign operator
>>>=            // binary bitwise right rotate and assign operator
.               // post-unary dereference operator
'               // pre/post-unary literal start/end operator
"               // pre/post-unary literal start/end operator
as              // binary safe type conversion operator
unsafe as       // binary unsafe type conversion operator
()              // pre/post-unary function invocation operator
[]              // pre/post-unary array access operator
countof         // pre-unary count operator
                // (returns the number elements in a type or the total reference
                // count for a `handle` / `hint`, or `strong` / `weak` pointer)
overhead        // pre-unary overhead operator
                // (obtains a pointer to the overhead information for a pointer,
                // `own`, `handle`, `hint`, `strong`, or `weak` pointer or
                // optional type)
overheadof      // pre-unary overhead sizing operator
                // (return the number of bytes overhead is needed for this type)
                // i.e. typically the size of a control block
allocatorof     // pre-unary allocator operator
                // (returns the allocator instance used to allocate an instance)
````


#### Non overloadable

````
*                 // post-unary pointer type declaration
&                 // post-unary reference type declaration, or
                  // pre-unary capture by reference operator
@                 // unary/binary standard allocator operator
                  // (allocate using the standard allocator and construct type)
@@                // unary/binary parallel allocator operator
                  // (allocate using the parallel allocator and construct type)
@!                // unary/binary synchronous allocator operator (allocate using
                  // the synchronous allocator and construct type)
.                 // binary namespace resolution operator
,                 // post-unary argument operator
;                 // binary statement separator and combiner operator
;;                // binary sub-statement separator operator
:                 // binary variable type declaration operator
::                // binary data type or meta-type declaration operator
?                 // post-unary optional type operator
??                // binary ternary operator (combined with sub-statement
                  // separator operator `;;`)
???               // unary uninitialized type operator
>>                // binary function composition
|>                // binary function invocation chaining
->                // post-unary argument combine operator (combine remaining
                  // function result arguments into a single automatically
                  // defined type)
<-                // pre-unary argument split operator (split type into
                  // multiple function arguments)
\                 // post-unary statement continuation operator
outerof           // binary outer type instance of operator (convert from
                  // contained `type` pointer to container `type` pointer safely
                  // via a managed type's RTTI)
lifetimeof        // shared lifetime operator (binds a raw pointer to an
                  // existing `strong` or `handle` pointer and safely checks if
                  // the pointer to a type points to memory within the allocated
                  // `strong` or `handle` pointer)
unsafe outerof    // binary unsafe outer type casting operator (convert from
                  // contained type pointer to container type pointer)
unsafe copyas     // binary unsafe `Unknown` copy casting of a function pointer
                  // (treat an `Unknown` pointer as pointing to an instance of a
                  // casted function `type` and make a copy of captured
                  // function contents)
unsafe lifetimeof // binary unsafe shared lifetime casting operator (converts a
                  // raw pointer to share a lifetime with an existing `strong`
                  // or `handle` pointer)
sizeof            // pre-unary size of operator (returns the size of a type in
                  // bytes)
alignof           // pre-unary align of operator (return the alignment of a
                  // type in modulus bytes)
offsetof          // binary offset of operator (compute the byte offset of a
                  // contained variable from a container type or container
                  // variable)
typeof            // pre-unary obtain the meta-data information of a variable,
                  // or expression, or `type`
````

Other expressions (cannot be overloaded):
````
#               // unary discard operator
$               // pre-unary templated argument declaration
...             // unary variadic values (array of optional variable arguments)
$...            // unary variadic types (array of types of variadic values)
()              // pre/post-unary function argument declaration operator
[]              // pre/post-unary array subscript operator
{               // pre-unary scope begin
}               // post-unary scope end
[{              // pre-unary multiple value declaration begin
}]              // post-unary multiple value declaration end
.               // pre-unary named variable and argument initialization
[[              // pre-unary compiler directive or attribute declaration open
]]              // post-unary compiler directive or attribute declaration close
_               // unary pointer to self (a type's pointer to itself within a
                // type's function)
___             // unary pointer to the current context
+++             // unary constructor
---             // unary destructor
````


### Compiler directives

````
align               // align contained types to a zero modulus address boundary
asset               // copy asset to built bundle
asynchronous        // indicates a function not normally considered to operate
                    // asynchronously may be performed asynchronously
compiler            // compiler name as a string literal 
compiles            // if a code block that follows compiles then a `true` is
                    // replaced otherwise a `false` is replaced
concept             // declare a function as a compile time check for
                    // input/output argument type checks within a meta-function
date                // date of compile as a literal
deprecate           // declare API sections as being deprecated
error               // cause an error in compilation
execute             // evaluates code blocks at compile time
export              // make type, variable, and other declarations visible to
                    // module importation
file                // indicates the source for a generated file
git                 // directives specific to git literals
function            // current function as a string literal
inline              // tells compiler to generate a function call as inline code
                    // rather than as a call to function
likely              // indicates a code path is more likely to be executed (for
                    // compiler and CPU optimization)
line                // indicates a source line for a generated file
location            // the code URL location as a string literal
lock-free           // disable lock generation around `once` values
panic               // control panic behavior in code generation
reserve             // reserve non-accessible unused space in a type
void                // declared a contained value occupies a location within a
                    // `type` without allocating space for the contained value
resolve             // controls when a declaration should resolve
return              // controls code generation for the return statement
source              // loads a related source file
tab-stop            // sets the tab-stop for the source that follows
time                // time of compile as a string literal
unlikely            // indicates a code paths is less likely to execute (for
                    // compiler and CPU optimization)
version-compiler    // compiler version as a string literal
version-import      // module import version as a string literal 
warning             // display a warning or control compiler warning behaviors
````


### Recommended naming conventions

````zax
// variables are recommended in lower camelCase and type names are recommended
// in upper CamelCase
variableName : TypeName

// functions are recommended in lower camelCase
funcName : ()() = {
    // ...
}

// function variables when used as as function prototypes are recommended in
// upper CamelCase
FunctionPrototype final : ()()

// scope names are recommended with lower_case_with_underscores
scope my_scope {
}

// namespaces are recommended in upper CamelCase
variableName : MyModuleName.SubType

// enum names and enum values are recommended in upper CamelCase
Fruit :: enum {
    Apple,
    Banana
}

// false abbreviations are highly discouraged
notGood : WrdsNotKnwnAbbr
vecList : VecIsShortForVector
````


### Type declaration

````zax
:: import Module.System.Types

// Use Pascal style variable declaration where the variable name is
// specified followed by the type
variableName : TypeName

// Types are declared using the `type` keyword
TypeName :: type {
    variableName : Integer
}

// A type can be assumed based on the value rather than requiring explicit
// declaration
assumedType := funcReturningType()

// Types can be deduced based on automatic type deduction when a variable is
// used in place of a type name
originValue : Integer
valueBorrowsOriginalValuesType : originalValue

// Symbols that start with _ are reserved for compiler and toolchain generated
// symbols and may contain additional underscores where needed thus are
// entirely reserved and must not be used by a programmer as a prefix.
_reservedVariableName
_ReservedTypeName

// Symbols ending with _ are discouraged except in the case of disambiguating
// keywords from symbols.
as_ // e.g. `as` is a keyword, but `as_` is not a keyword.

MyType :: type {
    m_no := 0   // NOT recommended as this Zax is a data oriented and not an
                // object or classification oriented language

    _no := 0    // NOT recommended (Zax has built in disambiguation)
    no_ := 0    // NOT recommended (Zax has built in disambiguation)

    // This pointer for the current type's instance is reserved as a single
    // underscore `_` and type variables can be distinguished from arguments by
    // using `_.value` vs a locally declaration of `value`.

    value : Integer

    function final : ()() = {
        value : Integer

        _.value = 1    // MyType's value is set to 1
        value = 1      // local value is set to 1
    }
}
````


### Intrinsic types

````zax
// import the module system types into the global namespace
:: import Module.System.Types

unknown : Unknown   // used as a generic pointer type to an `Unknown` type
nothing : Nothing   // used as a generic type instance of `Nothing`
void : Void         // an alias of the `Unknown` type
boolean : Boolean   // A value representing `true` or `false` literals

// aliased or templated signed integers to the appropriate fixed size equivalent
char : Char         // Signed value representing the size of a single string
                    // character (minimum 8 bits)
wchar : WChar       // Signed value representing the size of a wide string
                    // character (minimum 32 bits)
small : Small       // Small is a signed value of the smallest cpu type
                    // (minimum 8 bits)
short : Short       // Short is a signed value of a smaller cpu type
                    // (minimum 16 bits)
integer : Integer   // Integer is a signed value of the fastest cpu type
                    // (minimum 16 bits, 32/64 is typical)
long : Long         // Long is a signed value of the natural longest cpu type
                    // (minimum 32 bits)
longest : Longest   // Longest is a signed value of the largest cpu type
                    // (minimum 64 bits)

// unsigned versions of the signed integers
uchar : UChar
uwchar : UWChar
usmall : USmall
ushort : UShort
uinteger : UInteger
ulong : ULong
ulongest : ULongest

// aliased fastest signed integer type with at least the specified bit size 
fastI8 : FastI8
fastI16 : FastI16
fastI32 : FastI32
fastI64 : FastI64
fastI128 : FastI128

// aliased fastest unsigned integer type with at least the specified bit size 
fastU8 : FastU8
fastU16 : FastU16
fastU32 : FastU32
fastU64 : FastU64
fastU128 : FastU128

// fixed size signed integers
i8 : I8
i16 : I16
i32 : I32
i64 : I64
i128 : I128

// fixed size unsigned integers
u8 : U8
u16 : U16
u32 : U32
u64 : U64
u128 : U128

// familiar named types
byte : Byte             // alias of U8
dbyte : DByte           // alias of U16
qbyte : QByte           // alias of U32
obyte : OByte           // alias of U64

word : Word             // alias of the unsigned CPU's natural unit of data
                        // mapped to the U8 through U128 types (or the next
                        // largest size up if the natural word size is not
                        // on a power of 2 byte boundary)
                        // (undefined if greater than 128 bits)
dword : DWord           // alias of double the byte capacity of the Word type
                        // (undefined if greater than 128 bits)
qword : QWord           // alias of double the byte capacity of the DWord type
                        // (undefined if greater than 128 bits)

uuid : UUID             // alias of U128

rune : Rune             // alias of UWChar

// aliased fixed size sizing types
uptr : UPointer         // unsigned integer large enough to hold the
                        // value of pointer

sptr : SPointer         // signed integer large enough to hold the pointer
                        // difference between two pointer values using
                        // the same memory arena and must be the same bit size
                        // as a UPointer (although this type can overflow if
                        // the distances between the smallest pointer and the
                        // largest pointer exceed the capacity of the type)

slongPtr : SLongPointer // signed integer large enough to hold the pointer
                        // difference between two pointer positions even if the
                        // largest casted UPointer and smallest casted
                        // UPointer are used (this type must not cause a panic
                        // if the `as` operator is used to cast from a
                        // `UPointer` to a `SLongPointer`)

typeSize : TypeSize     // unsigned integer large enough to hold the size of
                        // the largest possible type or standard requested
                        // byte allocation

// aliased or meta-types floats mapping to fixed size type equivalents
float : Float           // fastest precision float
                        // (minimum 16 bits, 32/64 is typical)
half : Half             // half precision float (minimum 16 bit)
single : Single         // single precision float (minimum 32 bit)
double : Double         // double precision float (minimum 64 bit)
quadruple : Quadruple   // quadruple precision float (minimum 128 bit)

// fixed size floats
f16 : F16
f32 : F32
f64 : F64
f128 : F128

// advanced note: Integer/Float types are actually a templated type which
// contains a bit size and/or a sign, e.g.
Integer $(BitCount = Cpu.Integer.Optimal, UseSign = Sign.Signed) :: type {
    // ...
}
Float $(BitCount = Cpu.Integer.Optimal) :: type { /*... */ }

// strings have a built-in `length` `mutator` and are extended ASCII by default
stringA : String = "hello"
stringB := "type is implied"

// other string encodings are supported
utf8String : Utf8String = utf8'© Snowman Industries (☃)'
wideString : WString = w'hello'
````


### Intrinsic system literals

Literals are any constant literal value that requires compile time conversion from the input value to the underlying `type`.

Normal strings are enclosed with single quotes `'string'`, or double quotes `"string"`. The difference between single and double quote is merely symantec with the single quote being used as the preferred convention. Any escape sequencing interpretation performed inside any literal is entirely dependent on the type of a literal used where both single and double quotes utilize the same escaping interpretation logic for a given literal `type`. Literals are not required to support any escape sequences at all. The default `ascii` string type is applied to strings when a literal type is not specified, and no previous literal type declaration is being continued.

The type of a literal is prefixed before the value contained in single (`'`) or double (`"`) quotes. The language has support for some built-in literal types such as `a`, `ascii`, `utf8`, `c`, `w`, `unicode`, `b64`, `b`, `h` and others. The language is flexible and new compile time literals can be added to the language. A literal type specification is done by a literal type prefix before a value (`type'value'`). A single quote (`'`) can be embedded into a literal value by utilizing double quotes (`""`) around a literal's value and double quotes can be embedded into a literal value by using single quotes, e.g. `c'"'` and `c"'"`.

Literals can be extended across multiple lines using the continuation operator `\` and can interchange between using the single quote (`'`) and the double quote (`"`) as needed. The declaration of a prefixed literal type is only required initially when specifying a non-default literal type, or when the literal type's encoding changes (e.g. embedded specific wide character by specifying its hex value inside a wide character string would require a `w` literal prefix to be re-declared after using a `h` literal prefix when part of the same string literal sequence).

````zax
// import the module system literals into the global namespace
:: import Module.System.Literals

char1 := c'A'                       // becomes the ASCII letter `A`
char2 := c'\n'                      // C-style escapes supported thus becomes
                                    // an ASCII new line character
wideChar := r'😀'                    // becomes a rune character/wide character
charBackslash := c'\\'              // becomes a single backslash
charDoubleQuote := c'"'             // becomes a double quote
charSingleQuote := c"'"             // becomes a single quote
charNulValue := c'\0'               // becomes an ASCII NUL character
binary := b'1011101'                // becomes a base-2 number
binarySequence := 'm' b'1011101'    // becomes a string sequence with an
                                    // embedded character expressed in base-2
octal := o'12345670'                // becomes a base-8 number
octalSequence := 'n' o'76'          // becomes a string sequence with an
                                    // embedded character expressed in base-8
duodecimal := d'1234567890AB'       // becomes a base-12 number
duodecimalSequence := 'y' d'AB'     // becomes a string sequence with an
                                    // embedded character expressed in base-12
hexadecimal := h'ABC123'            // becomes a base-16 number
hexadecimalSequence := 'z' h'EF'    // becomes a string sequence with an
                                    // embedded character expressed in base-16
mixedSequence := c'h' 'e' \         // becomes a string sequence containing the
                 b'01101100' \      // ASCII characters "hello"
                 b'01101100' \
                 o'157'

runeString : Rune = h'0398' '03A4'  // wide character string consisting of
                                    // the unicode characters Theta and Tau.

namespacedType := Module.System.Literals.ascii"hello there!"

string := 'Default is an ASCII string type where c-style escapes are ' \
          'not supported.'

asciiString1 := a'an ASCII string with c-style escapes\n'
asciiString2 := ascii'an ASCII string without c-style escapes'

wideString1 := w'a wide string with escapes with c-style escapes\n'
wideString2 := unicode'a wide string without c-style escapes'

utf8String := utf8'utf8 string does not have escape sequences ' \
              'as remains "as is".'

// Convert from base-64 directly to an ASCII-string
// (embedded NUL characters can exist within strings)
base64 := b64'VGhlIHF1aWNrIGJyb3duIGZveC4='

// Convert from xml entity text directly to a wide-character string
xmlEntity := xml'John&#39;s Fish &amp; Chip Caf&#233;.'

// string literals merging with other literals
mergedLiterals := "This string has no escape sequences thus C:\file\paths is " \
                  "preserved as well as 'single quotes' as ASCII.'\n'

anotherWayToEscape := a'This string has escape sequences where backslashes'\
                      'need escaping for paths like C:\\file but "double'\
                      'quotes" are left "as is"\n'

beCarefulSingleQuotes := ascii'single quotes cannot exist inside single quotes'\
                         ' thus use "double quotes"' "around 'single quotes'\n"

wideStringLiterals := unicode'Wide string has no escapes but can embed wide ' \
                      'values such as "' h'16F' w'" fairly easily.\n'

continueExisting := w'encoding inside single quotes is continued ' \
                    'without needing to redeclare the encoding so long ' \
                    'as no change in encoding has occurred since the last ' \
                    'open and close single quote sequence but need ' \
                    'declaring again when changed, e.g. "' \
                    h'16F' w'" needed the "w" declared once again'
````

### Intrinsic Namespaces

The language defines a default namespace named `Module`. The namespace is the root namespace for all types relative to any current namespace. See [namespacing](namespacing.md) for more details.
