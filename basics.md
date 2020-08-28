
# [Zax Programming Language](index.md)

## Basics

### Basics of parsing

````zax
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
compiles
continue
collect
constant
deep
default
defer
discard
do
else
extension
execute
except
export
for
foreach
handle
hidden
if
in
is
immutable
import
keyword
managed
mutable
mutator
once
operator
override
own
private
promise
range
return
requires
scope
soa
suspend
switch
task
type
union
weak
while
void
yield
````


#### Keyword aliasing

Zax supports keyword aliasing using the declaration `alias keyword`. The verbose nature of the default keywords are made to ensure the language is natural rather than using shortened keywords that are inconsistently applied to the language. However, the keywords can be aliased to friendly names to type. The same reserved status applies to any aliased keywords within the context of where the original keyword would be allowed. An alias does not remove the original keyword from being available.

A word of caution: the temptation can be to use very short abbreviated words to save on typing but reading a language should be natural, not lead to too many variable name conflicts, and be consistent and non-confusing. For projects shared publicly, this can even be a greater source of confusion if different projects use different keywords and may be difficult for newcomers to learn new keyword standards. Besides, modern editors typically have common word completion.

Common aliases are placed in `Module.System.Keywords`:

````zax
// important common keywords that should be familiar to existing programmers
:: import Module.System.Keywords

// some example keywords that are aliased in the keywords module
const export :: alias keyword constant
````


#### Operator aliasing

Zax allows operators to become aliased into keywords using the declaration `alias operator keyword`. This allows natural language keywords to be applied to operators should a natural language alternative to the operator be desirable. Whenever the aliased keyword is seen, the operator is automatically substituted.

A small caveat: comments are not operators and cannot be aliased. The compiler treats comments as a first pass filtering of text meant for humans and they are not treated as part of the language (even where a compiler might capture the comments for documentation purposes).

````zax
add :: alias operator keyword +
not :: alias operator keyword !

value := 1 add 1
isTrue := not (value != 2)
````


#### Keyword disambiguation

Keywords are reserved only within the context of where the keyword is allowed. To disambiguate keywords from variables, variables can be postfixed with an underscore (`_`).

````zax
constant_ : Type constant
````


### Operators

#### Overloadable
````zax
+           // plus
-           // minus
++          // unary increment (pre or post)
--          // decrement (pre and post)
*           // multiple or unary pointer
/           // divide
%           // modulus divide
=           // assign
^           // binary xor
&           // binary and
|           // binary or
<<          // binary left shift
>>          // binary right shift
<<<         // binary left rotate
>>>         // binary right rotate
~           // one's compliment
!           // unary logical not
&&          // logical and
||          // logical or
^^          // logical exclusive or
+=          // add and assign
-=          // subtract and assign
*=          // multiply and assign
/=          // divide and assign
%=          // modulus divide and assign
==          // is equal
!=          // is not equal
<           // is less than
>           // is greater than
<=          // is less than
>=          // is greater than
^=          // binary xor and assign
|=          // binary or and assign
<<=         // binary left shift and assign
>>=         // binary right shift and assign
<<<=        // binary left rotate and assign
>>>=        // binary right rotate and assign
'           // literal start or end
as          // safe conversion to type
[           // array declaration open or array access
]           // array declaration close or array access
countof     // unary operator to returns the number elements in a type or
            // the total reference count for a
            // `strong`, `weak`, or `handle` pointer
````


#### Non overloadable
````zax
*           // pointer type declaration or unary pointer dereference
&           // reference type declaration or unary reference
+++         // unary constructor
---         // unary destructor
$           // templated variable, type or argument
@           // allocate and construct type
_           // this pointer (value can be changed to compatible type)
___         // serial context pointer (value can be changed)
.           // type/namespace resolution or dereference access
...         // variadic values
$...        // variadic type(s)
,           // argument
;           // statement separator
:           // variable type declaration
::          // data type or meta data declaration
::.         // data type dereference
?           // optional type
??          // ternary operator
???         // uninitialized type
{           // scope or  begin
}           // scope end
[[          // code directive or attribute declaration open
]]          // code directive or attribute declaration close
(           // function argument or function invocation start
)           // function argument or function invocation start
"           // string start or end
->          // combine remaining function result arguments into a
            // single automatic defined type
<-          // split type into multiple function arguments
\           // statement continuation
cast        // conversion from one type to another
            // (unsafe forcefully)
outercast   // convert from contained type pointer to container type pointer
            // (unsafe forcefully)
copycast    // treat the raw `void` pointer as pointing to an instance of the
            // casted type and make a copy of the contents
            // (unsafe forcefully)
lifecast    // converts a raw pointer to share a lifetime an existing
            // `strong` or `handle` pointer
            // (unsafe forcefully)
handlecast  // converts a raw pointer to an existing `handle` pointer
            // (unsafe forcefully)
outerlink   // convert from contained type pointer to container type pointer
            // (safely probing the instance if the conversion can happen)
lifelink    // links a raw pointer to an existing `strong` or `handle` pointer
            // (safely probes if the pointer to type points to memory within
            // the allocated `strong` or `handle` pointer)
sizeof      // unary operator to return the size of a type
alignof     // unary operator to return the alignment of a type
offsetof    // compute the byte offset of a contained type
            // within a container type
````


### Recommended naming conventions

````zax
// variables are recommended lower camelCase as well as type a type names as upper CamelCase
variableName : TypeName

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

void : Void         // An empty type
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
    //...
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

// binary numbers
char := c'\n'                   // becomes a new line character
charBackslash := c'\\'          // becomes a backslash
charDoubleQuote := c'"'         // becomes a double quote
charSingleQuote := c"'"         // becomes a single quote
charNulValue := c'\0'           // becomes a NUL character
binary := b'1011101'            // becomes a base-2 number
hexadecimal := h'ABC123'        // becomes a base-16 number
duodecimal := d'1234567890AB'   // becomes a base-12 number
octal := o'12345670'            // becomes a base-8 numbers

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


### Intrinsic system literal conversion

Zax does not perform type conversion, promotion or demotion of intrinsic compatible types. Casting operators `as` or `cast` must be used to convert from one intrinsic type to another type.

The `as` operator and the `cast` operator work in similar manners. Both convert an intrinsic value from one type to another. With intrinsic types, the `as` operator should cause a panic situation if the data would overflow if converted from a source type to a destination type. Whereas with intrinsic types, the `cast` operator will not panic ever when converting from one type to another but can cause either loss of information in an overflow, or data corruption / undefined behavior in another extreme. The `as` attempts to do a compatible conversion whereas a `cast` will treat the types as compatible even when they are not compatible.

````zax
i8 := -127 as I8                    // `as` can convert the value into an
                                    // I8 type without overflow
u8 := 255 as U8                     // `as` can convert the value into a
                                    // U8 type without overflow

u8Error := 256 as U8                // `as` will cause compile time
                                    // error as value is overflow
u8CastError := 256 cast U8          // `cast` will succeed despite the overflow
                                    // and the overflow is truncated

value : Integer = 256
u8Panic := value as U8              // `as` will cause runtime panic as
                                    // value is overflow
u8CastIgnorePanic := value cast U8  // `cast` will ignore overflow and the
                                    // overflow value is truncated

// Converting from a `WString`to a `String` is not always safe
// (but this case is safe)
string := w'hello' as String

// An unsafe conversion will cause a compilation error
stringError := w'this embedded literal "' h'16f' \
               w'" is not convertible' as WString

// converting from a `String` to a `WString` is always safe
string := 'always safe no matter what value'
wString := string as WString

// Converting from a `Utf8String` to a `WString` can cause runtime
// panic errors if the value contains an illegal sequence
utf8String := utf8'© Snowman Industries (☃)'

// This can overflow and cause panic if the utf8 contains
// illegal sequences (but won't in this case)
wString := utf8String as WString

// Any overflows will be ignored and will not cause panic for an
// illegal sequences
wString := utf8String cast WString


wideString := w'Runtime value with a non-ascii value "' h'16f' w'" is not ' \
              'legal to express in an ascii string.'

// This will cause a runtime panic since it cannot be converted
// (because the `as` keyword assumes all conversion is entirely legal)
stringIsRuntimePanic := wideString as String

// The overflow will be ignored during the conversion and the
// overflow value is truncated
StringOverflowIsIgnored := wideString cast String

// The wide string is convertible safely into a utf8 string as an no overflow
// is possible
stringIsRuntimeSafe := wideString as Utf8String
````
