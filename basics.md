
# [Zax Programming Language](index.md)

## Basics

### Basics of parsing

````zax
//---------------------------------------------------------------------------//
// C++ style inline comments

/*
   C style multiline comments
*/

// The semi-colon `;` is not necessary between statements, but can be used to
// split statements on a single line
funcA()
funcB()

// single line with multiple statements
funcA(); funcB()

value := 2 + func() \    // continuation of statement to multiple lines
         * 3 \
         / 2

// scope of statements treated as a single statement
{
    funcA()
    funcB()
}

// keywords are all lowercase and are reserved
if condition {
} else {
}

// Additional nested multiline comments
/**
  Nested multi
/**
  multi line comments
**/
  comments
**/
````


### Keywords

````zax
aos
alias
atomic
await
break
build
case
channel
continue
collect
constant
deep
default
defer
discard
each
else
extension
except
export
false
for
forever
handle
hidden
hint
if
in
is
immutable
import
inconstant
keyword
lazy
managed
mutable
mutator
once
operator
override
own
private
promise
redo
return
requires
scope
soa
suspend
switch
task
true
type
union
until
using
varies
void
weak
while
yield
````


#### Keyword aliasing

Zax supports keyword aliasing using the declaration `alias keyword`. The verbose nature of the default keywords are made to ensure the language is natural rather than using shortened keywords that are inconsistently applied to the language. However, the keywords can be aliased to friendly names to type. The same reserved status applies to any aliased keywords within the context of where the original keyword would be allowed. An alias does not remove the original keyword from being available.

Aliased keywords must be declared prior to usage and they are scoped to the module (unless exported). Attempting to use an aliased keywords prior to declaration will cause the compiler to treat the symbolic name as a normal symbolic declaration instead of its replacement alias.

A word of caution: the temptation can be to use very short abbreviated words to save on typing but reading a language should be natural, not lead to too many variable name conflicts, and be consistent and non-confusing. For projects shared publicly, this can even be a greater source of confusion if different projects use different keywords and may be difficult for newcomers to learn new keyword standards. Besides, modern editors typically have common word completion.

Common aliases are placed in `Module.System.Keywords`:

````zax
// important common keywords that should be familiar to existing programmers
[[resolve=now]] :: import Module.System.Keywords
````

From the `Module.System.Keywords`
````
// some example keywords that are aliased in the keywords module
const export :: alias keyword constant
property export :: alias keyword mutator
static export :: alias keyword once
downcast export :: alias keyword outercast
````


#### Operator and expression aliasing

Zax allows operators and expressions to become aliased into keywords using the declaration `alias operator keyword`. This allows natural language keywords to be applied to operators should a natural language alternative to the operator be desirable. Whenever the aliased keyword is seen, the operator is automatically substituted.

Aliased operators and expressions must be declared prior to usage and they are scoped to the module (unless exported). Attempting to use an aliased operator prior to declaration will cause the compiler to treat the operator as a symbolic name instead of its replacement alias.

A small caveat: comments are not operators and cannot be aliased. The compiler treats comments as a first pass filtering of text meant for humans and they are not treated as part of the language (even where a compiler might capture the comments for documentation purposes).

````zax
add :: alias operator keyword +
not :: alias operator keyword !
ellipses :: alias operator keyword ...
this :: alias operator keyword _

value := 1 add 1
isTrue := not (value != 2)

// "ellipses" becomes `...`
print : ()(ellipses) = {
    // ...
}

MyType :: type {
    value : Integer

    func final : ()(value : Integer) = {
        // `this` is not a keyword, it's an alias of `_`
        this.value = value

        // functionally equivalent to the above statement
        _.value  value
    }
}
````


#### Keyword disambiguation

Keywords are reserved only within the context of where the keyword is allowed. To disambiguate keywords from variables, variables can be postfixed with an underscore (`_`).

````zax
constant_ : Type constant
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
*               // multiply operator
/               // divide operator
%               // modulus divide operator
=               // assign operator
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
==              // binary is equal operator
!=              // binary is not equal operator
<=>             // binary three-way comparison operator
<               // binary is less than operator
>               // binary is greater than operator
<=              // binary is less than operator
>=              // binary is greater than operator
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
'               // literal start or end operator
"               // literal start or end operator
as              // safe type conversion operator
()              // function argument declaration operator or
                // function invocation operator
[]              // array declaration operator or
                // array access operator
countof         // pre-unary count operator
                // returns the number elements in a type or
                // the total reference count for a
                // `handle` / `hint`, or `strong` / `weak` pointer
overhead        // pre-unary overhead operator
                // obtains a pointer to the overhead information for
                // an `own`, `handle`, `hint`, `strong`, or `weak` pointer or
                // optional type
overheadof      // pre-unary overhead sizing operator
                // return the number of bytes overhead is needed for this type
                // i.e. typically the size of a control block
allocatorof     // returns the allocator used for an allocated instance
````


#### Non overloadable

````
*               // post-unary pointer type declaration
&               // post-unary reference type declaration or
                // pre-unary capture by reference operator
@               // post-unary standard allocator operator
                // allocate using the standard allocator and construct type
@@              // post-unary parallel allocator operator
                // allocate using the parallel allocator and construct type
@!              // post-unary sequential allocator operator
                // allocate using the sequential allocator and construct type
.               // name resolution operator
,               // argument operator
;               // statement separator operator
;;              // sub-statement separator operator
:               // variable type declaration operator
::              // data type or meta data declaration operator
::.             // data type dereference operator
?               // post-unary optional type operator
??              // ternary operator
                // (combined with sub-statement separator operator `;;`)
???             // uninitialized type operator
>>              // function composition
|>              // function invocation chaining
->              // post-unary argument combine operator
                // combine remaining function result arguments into a
                // single automatic defined type
<-              // pre-unary argument split operator
                // split type into multiple function arguments
\               // statement continuation operator
cast            // pre-unary unsafe type conversion operator
outercast       // unsafe outer type cast operator
                // convert from contained type pointer to container type pointer
copycast        // pre-unary unsafe `Unknown` copy cast
                // treat the raw `Unknown` pointer as pointing to an instance of
                // the casted type and make a copy of the contents
lifetimecast    // unsafe shared lifetime cast operator
                // converts a raw pointer to share a lifetime with an existing
                // `strong` or `handle` pointer
outerof         // outer type instance of operator
                // convert from contained type pointer to container type pointer
                // (safely via a managed type's RTTI)
lifetimeof      // shared lifetimeof operator
                // binds a raw pointer to an existing `strong` or `handle`
                // pointer (safely checks if the pointer to a type points to
                // memory within the allocated `strong` or `handle` pointer)
sizeof          // pre-unary size of operator
                // returns the size of a type
alignof         // pre-unary align of operator
                // return the alignment of a type
offsetof        // offset of operator
                // compute the byte offset of a contained variable from a
                // container type or container variable
````

Other expressions:
````
$               // templated argument declaration
...             // variadic values
$...            // variadic types
{               // scope begin
}               // scope end
{{ "{{" }}              // value, array or by-name type initialization begin
{{ }}}}              // value, array or by-name type initialization end
[[]]            // compiler directive or attribute declaration
_               // reference to self
                // a type's pointer to itself within type functions
___             // context; a pointer to the context
+++             // unary constructor
---             // unary destructor
````


### Compiler directives

````
align               // align contained types to a zero modulus address boundary
asset               // copy asset to built bundle
compiler            // compiler name as a string literal 
compiles            // if a code block that follows compiles a `true` is
                    // replaced otherwise a false is replaced
date                // date of compile as a string literal
deprecate           // declare API sections as being deprecated
discard             // suppress warning on unused variable or
                    // non captured results
error               // cause an error in compilation
execute             // evaluates code blocks at compile time
file                // indicates the source for a generated file
function            // current function as a string literal
inline              // tells compiler to inline vs call functions
likely              // indicates which code paths are more likely
                    // (for optimization)
line                // indicates the source line for a generated file
lock-free           // disable lock generation around `once` values
panic               // control panic behavior in code generation
reserve             // reserve non-accessible unused space in a type
void                // declared contained value occupies
                    // a location within a type
                    // without allocating space for the contained value
resolve             // controls when a declaration should resolve
return              // controls code generation for the return statement
source              // loads a related source file
tab-stop            // sets the tab-stop for the source that follows
time                // time of compile as a string literal
unlikely            // indicates which code paths are less likely
                    // (for optimization)
version-compiler    // compiler version as a string literal
version-import      // module import version as a string literal 
warning             // display a warning or control compiler warning behaviors
````


### Recommended naming conventions

````zax
// variables are recommended lower camelCase as well as type a type names as upper CamelCase
variableName : TypeName

// function variables as prototypes are recommended using upper CamelCase
FunctionPrototype final : ()()

// scope names are recommended with lower_case_with_underscores
scope my_scope {
}

// namespaces are recommended in upper CamelCase
variableName : MyModuleName.SubType

// false abbreviations are discouraged
notGood : WrdsNotKnwnAbbr
````


### Type declaration

````zax
:: import Module.System.Types

// Uses pascal style variable declaration where the variable name is
// specified followed by the type
variableName : TypeName

// Types are declared using 
TypeName :: type {
    variableName : Integer
}

// The type can be assumed based on the value
assumedType := funcReturningType()

// Types can be deduced based on borrowing another variable's type
originValue : Integer
valueBorrowsOriginalValuesType : originalValue

// Symbols that start with _ are reserved but may contain underscores
// where needed.
_reservedVariableName
_ReservedTypeName

// Symbols ending with _ are discouraged except in the case of disambiguating
// keywords from symbols.
as_ // e.g. `as` is a keyword, but `as_` is not a keyword.

MyType :: type {
    m_no := 0   // NOT recommended as this is a data oriented type and
                // not an object or classification

    // This pointer for a type is reserved with a single underscore `_` and
    // type variables can be distinguished from arguments by using
    // `_.variable` vs a locally declared `variable`
    _no := 0    // NOT recommended
    no_ := 0    // NOT recommended
}
````


### Intrinsic types

````zax
// import the module system types into the global namespace
:: import Module.System.Types

unknown : Unknown   // used as a generic pointer type to an unknown type
nothing : Nothing   // used as a generic type of nothing
void : Void         // an alias of the Unknown type
boolean : Boolean   // A value representing true of false

// aliased or templated signed integers to the appropriate fixed size equivalent
char : Char         // Signed value representing the size of a
                    // single string character (minimum 8 bits)
wchar : WChar       // Signed value representing the size of a
                    // wide string character (minimum 32 bits)
small : Small       // Small is a signed value of the smallest cpu type
                    // (minimum 8 bits)
short : Short       // Short is a signed value of the smaller cpu type
                    // (minimum 16 bits)
integer : Integer   // Integer is a signed value of the fastest cpu type
                    // (minimum 16 bits, 32/64 is typical)
long : Long         // Integer is a signed value of the natural longest cpu type
                    // (minimum 32 bits)
longest : Longest   // Integer is a signed value of the largest cpu type
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

sptr : SPointer         // signed integer large enough to hold the
                        // pointer difference between two pointer values
                        // and must be the same bit size as a UPointer
                        // (although this type can overflow if the distances
                        // between the smallest pointer and the largest pointer
                        // exceed the capacity of the type)

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
Integer $(BitCount = Cpu.Integer.optimal, UseSign = Sign.signed) :: type {
    // ...
}
Float $(BitCount = Cpu.Integer.optimal) :: type { /*... */ }

// strings have a built-in `length` `mutator` and are extended ascii by default
stringA : String = "hello"
stringB := "type is implied"

// other string encodings are supported
utf8String : Utf8String = utf8'© Snowman Industries (☃)'
wideString : WString = w'hello'
````


### Intrinsic system literals

Literals are any constant literal value that requires compile time conversion from the input value to the underlying type. By convention, normal ASCII strings are enclosed with double quotes `"string"` but single quotes are also entirely legal such as `'string'`. The escape sequencing performed inside any string is entirely dependent on the type of the literal. The type of the literal is prefixed before the single (`'`) or double (`"`) quotes. The language has support for some built in literals such as `ascii`, `utf8`, `c`, `w`, `b64`, `b`, `h` and others but the language is flexible and new compile time literals can be added to the language. By convention, all strings except for ascii strings surround the value with `prefix'value'` unless the the particular scenario requires a single quote (`'`) to be embedded into the value where double quotes (`""`) can be used, e.g. `c"'"`.

Literals can be extended across multiple lines and can toggle between using the single quote (`'`) and the double quote (`"`) as needed. The declaration of the prefix is only required when the type encoding changes (e.g. embedded specific hex wide characters by hex value inside a wide character string would require the `w` prefix to be redeclared after using the `h` prefix as part of the same string sequence). By default, the ascii encoding scheme is assumed unless a specific prefix is specified or the literal is a continuation of a literal being defined.

````zax
// import the module system literals into the global namespace
:: import Module.System.Literals

char := c'\n'                   // becomes a new line character
wideChar := r`😀`               // declare a rune character / wide character
charBackslash := c'\\'          // becomes a backslash
charDoubleQuote := c'"'         // becomes a double quote
charSingleQuote := c"'"         // becomes a single quote
charNulValue := c'\0'           // becomes a NUL character
binary := b'1011101'            // becomes a base-2 number
octal := o'12345670'            // becomes a base-8 numbers
duodecimal := d'1234567890AB'   // becomes a base-12 number
hexadecimal := h'ABC123'        // becomes a base-16 number

asciiString := ascii'some ascii string'

utf8String := utf8'utf8 string does not have escape sequences ' \
              'as remains "as is".'

// Convert from base-64 directly to an ascii-string
// (embedded NUL characters can exist within strings)
base64 := b64'VGhlIHF1aWNrIGJyb3duIGZveC4='

// Convert from xml entity text directly to a wide-character string
xmlEntity := xml'John&#39;s Fish &amp; Chip Caf&#233;.'

// string literals merging with other literals
mergedLiterals := "This string has no escape sequences thus C:\file\paths is " \
                  "preserved as well as 'single quotes' as ascii.'\n'

anotherWayToEscape := ascii'This string has escape sequences where backslashes'\
                      'need escaping for paths like C:\\file but "double'\
                      'quotes" are left "as is"\n'

becarefulSingleQuotes := ascii'single quotes cannot exist inside single quotes'\
                         'thus use "double quotes"' "around 'single quotes'\n"

wideStringLiterals := w'Wide string has no escapes but can embed wide values' \
                      'such as"' h'16F' w'" fairly easily.\n'

continueExisting := w'encoding inside single quotes is continued ' \
                    'without needing to redeclare the encoding so long ' \
                    'as no change in encoding has occurred since the last ' \
                    'open and close single quote sequence but need ' \
                    'declaring again when changed, e.g. "' \
                    h'16F' w'" needed the "w" declared once again'
````
