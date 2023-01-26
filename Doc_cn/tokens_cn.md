{==+==}
# Tokens
{==+==}

{==+==}


{==+==}
Tokens are primitive productions in the grammar defined by regular
(non-recursive) languages.  Rust source input can be broken down
into the following kinds of tokens:
{==+==}
Token是常规(非递归)语言描述语法的原始产物。Rust源码输入能分解为以下种类的Token:
{==+==}


{==+==}
* [Keywords]
* [Identifiers][identifier]
* [Literals](#literals)
* [Lifetimes](#lifetimes-and-loop-labels)
* [Punctuation](#punctuation)
* [Delimiters](#delimiters)
{==+==}
* [Keywords] 关键字
* [Identifiers][identifier] 标识符
* [Literals](#literals) 字面值
* [Lifetimes](#lifetimes-and-loop-labels) 生命周期
* [Punctuation](#punctuation) 标点符号
* [Delimiters](#delimiters) 分割符号
{==+==}


{==+==}
Within this documentation's grammar, "simple" tokens are given in [string
table production] form, and appear in `monospace` font.
{==+==}
在本文档的语法中，"simple" tokens以 [string table production] "字符串表项" 的形式给出，并以 `monospace` "等宽" 字体出现。
{==+==}


{==+==}
[string table production]: notation.md#string-table-productions
{==+==}

{==+==}


{==+==}
## Literals
{==+==}
## 字面值
{==+==}


{==+==}
Literals are tokens used in [literal expressions].
{==+==}
字面值用于[literal expressions] "字面值表达式" tokens。
{==+==}


{==+==}
### Examples
{==+==}
### 示例
{==+==}


{==+==}
#### Characters and strings
{==+==}
#### 字符和字符串
{==+==}


{==+==}
|                                              | Example         | `#` sets\* | Characters  | Escapes             |
|----------------------------------------------|-----------------|------------|-------------|---------------------|
| [Character](#character-literals)             | `'H'`           | 0          | All Unicode | [Quote](#quote-escapes) & [ASCII](#ascii-escapes) & [Unicode](#unicode-escapes) |
| [String](#string-literals)                   | `"hello"`       | 0          | All Unicode | [Quote](#quote-escapes) & [ASCII](#ascii-escapes) & [Unicode](#unicode-escapes) |
| [Raw string](#raw-string-literals)           | `r#"hello"#`    | <256       | All Unicode | `N/A`                                                      |
| [Byte](#byte-literals)                       | `b'H'`          | 0          | All ASCII   | [Quote](#quote-escapes) & [Byte](#byte-escapes)                               |
| [Byte string](#byte-string-literals)         | `b"hello"`      | 0          | All ASCII   | [Quote](#quote-escapes) & [Byte](#byte-escapes)                               |
| [Raw byte string](#raw-byte-string-literals) | `br#"hello"#`   | <256       | All ASCII   | `N/A`                                                      |
{==+==}

{==+==}


{==+==}
\* The number of `#`s on each side of the same literal must be equivalent.
{==+==}
\* 同一字面值两侧 `#` 的数量必须相等。
{==+==}


{==+==}
#### ASCII escapes
{==+==}
#### ASCII 转义符
{==+==}


{==+==}
|   | Name |
|---|------|
| `\x41` | 7-bit character code (exactly 2 digits, up to 0x7F) |
| `\n` | Newline |
| `\r` | Carriage return |
| `\t` | Tab |
| `\\` | Backslash |
| `\0` | Null |
{==+==}

{==+==}


{==+==}
#### Byte escapes
{==+==}
#### Byte 转义符
{==+==}


{==+==}
|   | Name |
|---|------|
| `\x7F` | 8-bit character code (exactly 2 digits) |
| `\n` | Newline |
| `\r` | Carriage return |
| `\t` | Tab |
| `\\` | Backslash |
| `\0` | Null |
{==+==}

{==+==}


{==+==}
#### Unicode escapes
{==+==}
#### Unicode 转义符
{==+==}


{==+==}
|   | Name |
|---|------|
| `\u{7FFF}` | 24-bit Unicode character code (up to 6 digits) |
{==+==}

{==+==}


{==+==}
#### Quote escapes
{==+==}
#### Quote 转义符
{==+==}


{==+==}
|   | Name |
|---|------|
| `\'` | Single quote |
| `\"` | Double quote |
{==+==}

{==+==}


{==+==}
#### Numbers
{==+==}
#### 数字
{==+==}


{==+==}
| [Number literals](#number-literals)`*` | Example | Exponentiation |
|----------------------------------------|---------|----------------|
| Decimal integer | `98_222` | `N/A` |
| Hex integer | `0xff` | `N/A` |
| Octal integer | `0o77` | `N/A` |
| Binary integer | `0b1111_0000` | `N/A` |
| Floating-point | `123.0E+77` | `Optional` |
{==+==}

{==+==}


{==+==}
`*` All number literals allow `_` as a visual separator: `1_234.0E+18f64`
{==+==}
`*` 所有的数字字面值允许 `_` 作为可视化分隔符: `1_234.0E+18f64`
{==+==}


{==+==}
#### Suffixes
{==+==}
#### 后缀
{==+==}


{==+==}
A suffix is a sequence of characters following the primary part of a literal (without intervening whitespace), of the same form as a non-raw identifier or keyword.
{==+==}
后缀是在字面值主要部分之后的一串字符(中间没有空白)，其形式与非原始标识符或关键词相同。
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> SUFFIX : IDENTIFIER_OR_KEYWORD\
> SUFFIX_NO_E : SUFFIX <sub>_not beginning with `e` or `E`_</sub>
{==+==}

{==+==}


{==+==}
Any kind of literal (string, integer, etc) with any suffix is valid as a token.
{==+==}
任何种类的字面值 (字符串、整数等) 可带有任意后缀，将作为一个token使用。
{==+==}


{==+==}
A literal token with any suffix can be passed to a macro without producing an error.
The macro itself will decide how to interpret such a token and whether to produce an error or not.
In particular, the `literal` fragment specifier for by-example macros matches literal tokens with arbitrary suffixes.
{==+==}
带有任意后缀的字面值token可以传递给宏而不产生错误。
宏本身将决定如何解释这样的token以及是否产生错误。
特别是，by-example宏的 `literal` 片段 specifier "指示器"可以匹配具有任意后缀的字面值token。
{==+==}


{==+==}
```rust
macro_rules! blackhole { ($tt:tt) => () }
macro_rules! blackhole_lit { ($l:literal) => () }

blackhole!("string"suffix); // OK
blackhole_lit!(1suffix); // OK
```
{==+==}

{==+==}


{==+==}
However, suffixes on literal tokens which are interpreted as literal expressions or patterns are restricted.
Any suffixes are rejected on non-numeric literal tokens,
and numeric literal tokens are accepted only with suffixes from the list below.
{==+==}
然而，在被解释为字面值表达式或模式的字面值token的后缀是受到限制的。
会拒绝在非数字字面值token上的任意后缀。
而数字字面值token只接受以下列表中的后缀。
{==+==}


{==+==}
| Integer | Floating-point |
|---------|----------------|
| `u8`, `i8`, `u16`, `i16`, `u32`, `i32`, `u64`, `i64`, `u128`, `i128`, `usize`, `isize` | `f32`, `f64` |
{==+==}

{==+==}


{==+==}
### Character and string literals
{==+==}
### 字符和字符串字面值
{==+==}


{==+==}
#### Character literals
{==+==}
#### 字符字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> CHAR_LITERAL :\
> &nbsp;&nbsp; `'` ( ~\[`'` `\` \\n \\r \\t] | QUOTE_ESCAPE | ASCII_ESCAPE | UNICODE_ESCAPE ) `'` SUFFIX<sup>?</sup>
>
> QUOTE_ESCAPE :\
> &nbsp;&nbsp; `\'` | `\"`
>
> ASCII_ESCAPE :\
> &nbsp;&nbsp; &nbsp;&nbsp; `\x` OCT_DIGIT HEX_DIGIT\
> &nbsp;&nbsp; | `\n` | `\r` | `\t` | `\\` | `\0`
>
> UNICODE_ESCAPE :\
> &nbsp;&nbsp; `\u{` ( HEX_DIGIT `_`<sup>\*</sup> )<sup>1..6</sup> `}`
{==+==}

{==+==}


{==+==}
A _character literal_ is a single Unicode character enclosed within two
`U+0027` (single-quote) characters, with the exception of `U+0027` itself,
which must be _escaped_ by a preceding `U+005C` character (`\`).
{==+==}
_character literal_ 字符字面值是指被两个 `U+0027` (单引号) 字符包围的单个Unicode字符，
但 `U+0027` 本身除外，单引号必须在前面以 `U+005C` 字符 (`\`)  _escaped_ 转义。
{==+==}


{==+==}
#### String literals
{==+==}
#### 字符串字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> STRING_LITERAL :\
> &nbsp;&nbsp; `"` (\
> &nbsp;&nbsp; &nbsp;&nbsp; ~\[`"` `\` _IsolatedCR_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | QUOTE_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | ASCII_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | UNICODE_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | STRING_CONTINUE\
> &nbsp;&nbsp; )<sup>\*</sup> `"` SUFFIX<sup>?</sup>
>
> STRING_CONTINUE :\
> &nbsp;&nbsp; `\` _followed by_ \\n
{==+==}

{==+==}


{==+==}
A _string literal_ is a sequence of any Unicode characters enclosed within two
`U+0022` (double-quote) characters, with the exception of `U+0022` itself,
which must be _escaped_ by a preceding `U+005C` character (`\`).
{==+==}
_string literal_ 字符串字面值是由两个 `U+0022` (双引号) 字符包围的任意Unicode字符序列，
但 `U+0022` 本身除外，双引号必须在前面以 `U+005C` 字符 (`\`)  _escaped_ 转义。
{==+==}


{==+==}
Line-breaks are allowed in string literals. A line-break is either a newline
(`U+000A`) or a pair of carriage return and newline (`U+000D`, `U+000A`). Both
byte sequences are normally translated to `U+000A`, but as a special exception,
when an unescaped `U+005C` character (`\`) occurs immediately before a line
break, then the line break character(s), and all immediately following
` ` (`U+0020`), `\t` (`U+0009`), `\n` (`U+000A`) and `\r` (`U+0000D`) characters
are ignored. Thus `a`, `b` and `c` are equal:
{==+==}
字符串字面值中允许断行。换行符是换行符 (`U+000A`) 或一对回车和换行符 (`U+000D`, `U+000A`) ，
这两个字节序列通常被翻译成 `U+000A` ，但有一种特殊的例外，
当一个未转义的 `U+005C`  (`\`) 字符出现在换行之前，那么忽略断行符和所有紧随其后的 ` ` (`U+0020`) 、 `\t` (`U+0009`) 、 `\n` (`U+000A`) 、 `\r` (`U+0000D`) 字符。因此 `a` 、 `b` 、 `c` 相同:
{==+==}


{==+==}
```rust
let a = "foobar";
let b = "foo\
         bar";
let c = "foo\

     bar";

assert_eq!(a, b);
assert_eq!(b, c);
```
{==+==}

{==+==}


{==+==}
> Note: Rust skipping additional newlines (like in example `c`) is potentially confusing and
> unexpected. This behavior may be adjusted in the future. Until a decision is made, it is
> recommended to avoid relying on this, i.e. skipping multiple newlines with line continuations.
> See [this issue](https://github.com/rust-lang/reference/pull/1042) for more information.
{==+==}
> 注意: Rust 跳过额外的新行(比如在例子 `c` 中) ，这有可能会令人困惑和意外。 
> 在未来可能会调整这种行为。在做出决定之前，建议避免使用, 
> 也就是说，会跳过多个连续的换行。
> 更多内容见 [这个issue](https://github.com/rust-lang/reference/pull/1042) 。
{==+==}


{==+==}
#### Character escapes
{==+==}
#### 字符转义
{==+==}


{==+==}
Some additional _escapes_ are available in either character or non-raw string
literals. An escape starts with a `U+005C` (`\`) and continues with one of the
following forms:
{==+==}
可以在字符或非原始字符串字面值中使用一些额外的 _escapes_ 转义，
转义以 `U+005C` (`\`) 开始，并以下列形式延续:
{==+==}


{==+==}
* A _7-bit code point escape_ starts with `U+0078` (`x`) and is
  followed by exactly two _hex digits_ with value up to `0x7F`. It denotes the
  ASCII character with value equal to the provided hex value. Higher values are
  not permitted because it is ambiguous whether they mean Unicode code points or
  byte values.
{==+==}
* A _7-bit code point escape_ starts with `U+0078` (`x`) and is followed by exactly two _hex digits_ with value up to `0x7F`. It denotes the ASCII character with value equal to the provided hex value. Higher values are not permitted because it is ambiguous whether they mean Unicode code points or byte values.
* _7-bit code point escape_ "7比特编码转义" 以 `U+0078` (`x`) 开始，后面正好有两个数值不超过 `0x7F` 的 _hex digits_ "十六进制数字"。
  它表示ASCII字符，其值等于提供的十六进制值。不允许的更高的值，因为无法明确是指Unicode编码还是字节值。
{==+==}


{==+==}
* A _24-bit code point escape_ starts with `U+0075` (`u`) and is followed
  by up to six _hex digits_ surrounded by braces `U+007B` (`{`) and `U+007D`
  (`}`). It denotes the Unicode code point equal to the provided hex value.
* A _whitespace escape_ is one of the characters `U+006E` (`n`), `U+0072`
  (`r`), or `U+0074` (`t`), denoting the Unicode values `U+000A` (LF),
  `U+000D` (CR) or `U+0009` (HT) respectively.
{==+==}
* _24-bit code point escape_ "24位编码转义" 以 `U+0075` (`u`) 开始，后面是最多六个 _hex digits_ "十六位数字"，由大括号 `U+007B` (`{`) 和 `U+007D` (`}`) 包围。它表示Unicode编码，等于所提供的十六进制值。
* _whitespace escape_ "空白转义" 是字符 `U+006E` (`n`) 、 `U+0072` (`r`) 或 `U+0074` (`t`) 之一，分别表示Unicode值 `U+000A` (LF) 、 `U+000D` (CR) 或 `U+0009` (HT) 。
{==+==}


{==+==}
* The _null escape_ is the character `U+0030` (`0`) and denotes the Unicode
  value `U+0000` (NUL).
* The _backslash escape_ is the character `U+005C` (`\`) which must be
  escaped in order to denote itself.
{==+==}
* _null escape_ "空转义" 是字符 `U+0030` (`0`) ，表示Unicode值 `U+0000` (NUL) 。
* _backslash escape_ "反斜线转义" 是字符 `U+005C` (`\`) ，必须经过转义以表示自身。
{==+==}


{==+==}
#### Raw string literals
{==+==}
#### 原始字符串字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> RAW_STRING_LITERAL :\
> &nbsp;&nbsp; `r` RAW_STRING_CONTENT SUFFIX<sup>?</sup>
>
> RAW_STRING_CONTENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `"` ( ~ _IsolatedCR_ )<sup>* (non-greedy)</sup> `"`\
> &nbsp;&nbsp; | `#` RAW_STRING_CONTENT `#`
{==+==}

{==+==}


{==+==}
Raw string literals do not process any escapes. They start with the character
`U+0072` (`r`), followed by fewer than 256 of the character `U+0023` (`#`) and a
`U+0022` (double-quote) character. The _raw string body_ can contain any sequence
of Unicode characters and is terminated only by another `U+0022` (double-quote)
character, followed by the same number of `U+0023` (`#`) characters that preceded
the opening `U+0022` (double-quote) character.
{==+==}
原始字符串字面值不处理任何转义。
以字符 `U+0072` (`r`) 开始，后面是少于256个的 `U+0023` (`#`) 和 `U+0022` (双引号) 字符。
_raw string body_ "原始字符串主体" 可以包含任何Unicode字符序列，并且只能由另一个 `U+0022` (双引号) 字符结束，后面是 `U+0022` (双引号) 字符与开头相同数量的 `U+0023` (`#`) 字符。
{==+==}


{==+==}
All Unicode characters contained in the raw string body represent themselves,
the characters `U+0022` (double-quote) (except when followed by at least as
many `U+0023` (`#`) characters as were used to start the raw string literal) or
`U+005C` (`\`) do not have any special meaning.
{==+==}
原始字符串主体中包含的所有Unicode字符都表示自己。
字符 `U+0022` (双引号)  或 `U+005C` (`\`) 不具有任何特殊含义 (除非后面有至少相同数量的 `U+0023` (`#`) 字符用于表达原始字符串字面值)。
{==+==}


{==+==}
Examples for string literals:
{==+==}
字符串字面值示例:
{==+==}


{==+==}
```rust
"foo"; r"foo";                     // foo
"\"foo\""; r#""foo""#;             // "foo"

"foo #\"# bar";
r##"foo #"# bar"##;                // foo #"# bar

"\x52"; "R"; r"R";                 // R
"\\x52"; r"\x52";                  // \x52
```
{==+==}

{==+==}


{==+==}
### Byte and byte string literals
{==+==}
### Byte 和 byte 字符串字面值
{==+==}


{==+==}
#### Byte literals
{==+==}
#### Byte字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> BYTE_LITERAL :\
> &nbsp;&nbsp; `b'` ( ASCII_FOR_CHAR | BYTE_ESCAPE )  `'` SUFFIX<sup>?</sup>
>
> ASCII_FOR_CHAR :\
> &nbsp;&nbsp; _any ASCII (i.e. 0x00 to 0x7F), except_ `'`, `\`, \\n, \\r or \\t
>
> BYTE_ESCAPE :\
> &nbsp;&nbsp; &nbsp;&nbsp; `\x` HEX_DIGIT HEX_DIGIT\
> &nbsp;&nbsp; | `\n` | `\r` | `\t` | `\\` | `\0` | `\'` | `\"`
{==+==}

{==+==}


{==+==}
A _byte literal_ is a single ASCII character (in the `U+0000` to `U+007F`
range) or a single _escape_ preceded by the characters `U+0062` (`b`) and
`U+0027` (single-quote), and followed by the character `U+0027`. If the character
`U+0027` is present within the literal, it must be _escaped_ by a preceding
`U+005C` (`\`) character. It is equivalent to a `u8` unsigned 8-bit integer
_number literal_.
{==+==}
_byte literal_ "字节字面值" 表示是一个ASCII字符 (在 `U+0000` 到 `U+007F` 范围内) 或单一的 _escape_ "转义"，前面是字符 `U+0062` (`b`) 和 `U+0027` (单引号)，后面是字符 `U+0027` 。
如果字面值有 `U+0027` 字符，必须由前置 `U+005C` (`\`) 字符来 _escaped_ "转义"。其相当于一个 `u8` 无符号8位整数 _number literal_ 数字字面值。
{==+==}


{==+==}
#### Byte string literals
{==+==}
#### Byte 字符串字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> BYTE_STRING_LITERAL :\
> &nbsp;&nbsp; `b"` ( ASCII_FOR_STRING | BYTE_ESCAPE | STRING_CONTINUE )<sup>\*</sup> `"` SUFFIX<sup>?</sup>
>
> ASCII_FOR_STRING :\
> &nbsp;&nbsp; _any ASCII (i.e 0x00 to 0x7F), except_ `"`, `\` _and IsolatedCR_
{==+==}

{==+==}


{==+==}
A non-raw _byte string literal_ is a sequence of ASCII characters and _escapes_,
preceded by the characters `U+0062` (`b`) and `U+0022` (double-quote), and
followed by the character `U+0022`. If the character `U+0022` is present within
the literal, it must be _escaped_ by a preceding `U+005C` (`\`) character.
Alternatively, a byte string literal can be a _raw byte string literal_, defined
below. The type of a byte string literal of length `n` is `&'static [u8; n]`.
{==+==}
非原始的 _byte string literal_ "字节字符串字面值" 是一串ASCII字符和 _escapes_ "转义"。
前面是字符 `U+0062` (`b`) 和 `U+0022` (双引号) ，后面是字符 `U+0022` 。
如果字面值有 `U+0022` 字符，必须前置 `U+005C` (`\`) 字符来转义。
或者，字节字符串字面值是 _raw byte string literal_ ，定义如下。
长度为 `n` 的字节字符串字面值的类型是 `&'static [u8; n]` 。
{==+==}


{==+==}
Some additional _escapes_ are available in either byte or non-raw byte string
literals. An escape starts with a `U+005C` (`\`) and continues with one of the
following forms:
{==+==}
一些额外的 _escapes_ "转义" 在字节或非原始字节的字符串字面值中都是可用的。
转义以 `U+005C` (`\`) 开始，并以下列形式延续。
{==+==}


{==+==}
* A _byte escape_ escape starts with `U+0078` (`x`) and is
  followed by exactly two _hex digits_. It denotes the byte
  equal to the provided hex value.
* A _whitespace escape_ is one of the characters `U+006E` (`n`), `U+0072`
  (`r`), or `U+0074` (`t`), denoting the bytes values `0x0A` (ASCII LF),
  `0x0D` (ASCII CR) or `0x09` (ASCII HT) respectively.
{==+==}
* _byte escape_ "字节转义" 以 `U+0078` (`x`) 开始，后面正好有两个 _hex digits_ "十六进制数字" 。表示相当于所提供的十六进制值的字节。
* _whitespace escape_  "空白转义" 是字符 `U+006E` (`n`) 、`U+0072` (`r`) ，或 `U+0074` (`t`) ，分别表示字节值 `0x0A` (ASCII LF) 、 `0x0D` (ASCII CR) 或 `0x09` (ASCII HT) 。
{==+==}


{==+==}
* The _null escape_ is the character `U+0030` (`0`) and denotes the byte
  value `0x00` (ASCII NUL).
* The _backslash escape_ is the character `U+005C` (`\`) which must be
  escaped in order to denote its ASCII encoding `0x5C`.
{==+==}
* _null escape_ "null转义" 是字符 `U+0030` (`0`) ，表示字节值 `0x00` (ASCII NUL) 。
* _backslash escape_ "反斜线转义"  是字符 `U+005C` (`\`)  ，必须转义以表示其ASCII编码 `0x5C` 。
{==+==}


{==+==}
#### Raw byte string literals
{==+==}
#### 原始 byte 字符串字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> RAW_BYTE_STRING_LITERAL :\
> &nbsp;&nbsp; `br` RAW_BYTE_STRING_CONTENT SUFFIX<sup>?</sup>
>
> RAW_BYTE_STRING_CONTENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `"` ASCII<sup>* (non-greedy)</sup> `"`\
> &nbsp;&nbsp; | `#` RAW_BYTE_STRING_CONTENT `#`
>
> ASCII :\
> &nbsp;&nbsp; _any ASCII (i.e. 0x00 to 0x7F)_
{==+==}

{==+==}


{==+==}
Raw byte string literals do not process any escapes. They start with the
character `U+0062` (`b`), followed by `U+0072` (`r`), followed by fewer than 256
of the character `U+0023` (`#`), and a `U+0022` (double-quote) character. The
_raw string body_ can contain any sequence of ASCII characters and is terminated
only by another `U+0022` (double-quote) character, followed by the same number of
`U+0023` (`#`) characters that preceded the opening `U+0022` (double-quote)
character. A raw byte string literal can not contain any non-ASCII byte.
{==+==}
原始字节字符串字面值不处理任何转义。
它们以字符 `U+0062` (`b`) 开始，然后是 `U+0072` (`r`) ，后面是少于256的字符 `U+0023` (`#`)，以及一个 `U+0022` (双引号) 字符。
_raw string body_ "原始字符串主体" 可以包含任何ASCII字符序列，并且只能由另一个 `U+0022` (双引号) 字符终止，后面是 `U+0022` (双引号) 字符与开头数量相同 `U+0023` (`#`) 字符。
原始的字节字符串字面值不能包含任何非ASCII字节。
{==+==}


{==+==}
All characters contained in the raw string body represent their ASCII encoding,
the characters `U+0022` (double-quote) (except when followed by at least as
many `U+0023` (`#`) characters as were used to start the raw string literal) or
`U+005C` (`\`) do not have any special meaning.
{==+==}
原始字符串主体中包含的所有字符表示其ASCII编码。
字符 `U+0022` (双引号) 或 `U+005C` (`\`) 不具有任何特殊含义 (除非后面有至少相同数量的 `U+0023` (`#`) 字符表达原始字符串字面值）。
{==+==}


{==+==}
Examples for byte string literals:
{==+==}
字符串字面值示例:
{==+==}


{==+==}
```rust
b"foo"; br"foo";                     // foo
b"\"foo\""; br#""foo""#;             // "foo"

b"foo #\"# bar";
br##"foo #"# bar"##;                 // foo #"# bar

b"\x52"; b"R"; br"R";                // R
b"\\x52"; br"\x52";                  // \x52
```
{==+==}

{==+==}


{==+==}
### Number literals
{==+==}
### 数字字面值
{==+==}


{==+==}
A _number literal_ is either an _integer literal_ or a _floating-point
literal_. The grammar for recognizing the two kinds of literals is mixed.
{==+==}
_number literal_ 数字字面值是一个 _integer literal_ 整数字面值或一个 _floating-point literal__ 浮点字面值。语法上混合识别这两种字面值。
{==+==}


{==+==}
#### Integer literals
{==+==}
#### 整数字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> INTEGER_LITERAL :\
> &nbsp;&nbsp; ( DEC_LITERAL | BIN_LITERAL | OCT_LITERAL | HEX_LITERAL )
>              SUFFIX_NO_E<sup>?</sup>
>
> DEC_LITERAL :\
> &nbsp;&nbsp; DEC_DIGIT (DEC_DIGIT|`_`)<sup>\*</sup>
>
> BIN_LITERAL :\
> &nbsp;&nbsp; `0b` (BIN_DIGIT|`_`)<sup>\*</sup> BIN_DIGIT (BIN_DIGIT|`_`)<sup>\*</sup>
>
> OCT_LITERAL :\
> &nbsp;&nbsp; `0o` (OCT_DIGIT|`_`)<sup>\*</sup> OCT_DIGIT (OCT_DIGIT|`_`)<sup>\*</sup>
>
> HEX_LITERAL :\
> &nbsp;&nbsp; `0x` (HEX_DIGIT|`_`)<sup>\*</sup> HEX_DIGIT (HEX_DIGIT|`_`)<sup>\*</sup>
>
> BIN_DIGIT : \[`0`-`1`]
>
> OCT_DIGIT : \[`0`-`7`]
>
> DEC_DIGIT : \[`0`-`9`]
>
> HEX_DIGIT : \[`0`-`9` `a`-`f` `A`-`F`]
{==+==}

{==+==}


{==+==}
An _integer literal_ has one of four forms:
{==+==}
_integer literal_ "整数字面值" 四种形式之一:
{==+==}


{==+==}
* A _decimal literal_ starts with a *decimal digit* and continues with any
  mixture of *decimal digits* and _underscores_.
* A _hex literal_ starts with the character sequence `U+0030` `U+0078`
  (`0x`) and continues as any mixture (with at least one digit) of hex digits
  and underscores.
* An _octal literal_ starts with the character sequence `U+0030` `U+006F`
  (`0o`) and continues as any mixture (with at least one digit) of octal digits
  and underscores.
* A _binary literal_ starts with the character sequence `U+0030` `U+0062`
  (`0b`) and continues as any mixture (with at least one digit) of binary digits
  and underscores.
{==+==}
* _decimal literal_ 十进制字面值以一个 *十进制数字* 开始，然后以任何 *十进制数字* 和 _下划线_ 的混合。
* _hex literal_ 十六进制字面值以字符序列 `U+0030` `U+0078` (`0x`) 开始，然后以任何十六进制数字和下划线混合(至少有一个数字)。
* _octal literal_ 八进制字面值以字符序列 `U+0030` `U+006F` (`0o`) 开始，然后以任何八进制数字和下划线混合(至少有一个数字)。
* _binary literal_ 二进制字面值以字符序列 `U+0030` `U+0062` (`0b`) 开始，然后以任何二进制数字和下划线混合(至少有一个数字)。
{==+==}


{==+==}
Like any literal, an integer literal may be followed (immediately, without any spaces) by a suffix as described above.
The suffix may not begin with `e` or `E`, as that would be interpreted as the exponent of a floating-point literal.
See [literal expressions] for the effect of these suffixes.
{==+==}
像其他字面值一样，整数字可以在后面加一个后缀(紧接着，没有任何空格)，如上所述。
后缀不能以 `e` 或 `E` 开头，因为这将被解释为浮点字面值的指数。
关于这些后缀的实现，见字面值表达式 [literal expressions] 。
{==+==}


{==+==}
Examples of integer literals which are accepted as literal expressions:
{==+==}
字面值表达式允许的整数字面值的示例:
{==+==}


{==+==}
```rust
# #![allow(overflowing_literals)]
123;
123i32;
123u32;
123_u32;

0xff;
0xff_u8;
0x01_f32; // integer 7986, not floating-point 1.0
0x01_e3;  // integer 483, not floating-point 1000.0

0o70;
0o70_i16;

0b1111_1111_1001_0000;
0b1111_1111_1001_0000i64;
0b________1;

0usize;

// These are too big for their type, but are accepted as literal expressions.
128_i8;
256_u8;

// This is an integer literal, accepted as a floating-point literal expression.
5f32;
```
{==+==}
```rust
# #![allow(overflowing_literals)]
123;
123i32;
123u32;
123_u32;

0xff;
0xff_u8;
0x01_f32; // 整数 7986, 非浮点 1.0
0x01_e3;  // 整数 483, 非浮点 1000.0

0o70;
0o70_i16;

0b1111_1111_1001_0000;
0b1111_1111_1001_0000i64;
0b________1;

0usize;

// 这对于其类型来说值太大，但允许在字面值表达式。
128_i8;
256_u8;

// 这是整数字面值，允许在浮点字面值表达式。
5f32;
```
{==+==}


{==+==}
Note that `-1i8`, for example, is analyzed as two tokens: `-` followed by `1i8`.
{==+==}
注意，例如， `-1i8` 被解析为两个标记: `-` 和 `1i8`。
{==+==}


{==+==}
Examples of integer literals which are not accepted as literal expressions:
{==+==}
字面值表达式不允许的整数字面值示例:
{==+==}


{==+==}
```rust
# #[cfg(FALSE)] {
0invalidSuffix;
123AFB43;
0b010a;
0xAB_CD_EF_GH;
0b1111_f32;
# }
```
{==+==}

{==+==}


{==+==}
#### Tuple index
{==+==}
#### 元组索引
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> TUPLE_INDEX: \
> &nbsp;&nbsp; INTEGER_LITERAL
{==+==}

{==+==}


{==+==}
A tuple index is used to refer to the fields of [tuples], [tuple structs], and
[tuple variants].
{==+==}
元组索引用于指代 [tuples] 元组、[tuple structs] 元组结构和 [tuple variants] 元组变体的字段。
{==+==}


{==+==}
Tuple indices are compared with the literal token directly. Tuple indices
start with `0` and each successive index increments the value by `1` as a
decimal value. Thus, only decimal values will match, and the value must not
have any extra `0` prefix characters.
{==+==}
元组索引直接与字面值token进行对比。元组索引从 `0` 开始，每一个连续的索引其值增加 `1` ，为一个十进制值。因此，只有十进制的值才能匹配，其值不能有任何额外的 `0` 前缀字符。
{==+==}


{==+==}
```rust,compile_fail
let example = ("dog", "cat", "horse");
let dog = example.0;
let cat = example.1;
// The following examples are invalid.
let cat = example.01;  // ERROR no field named `01`
let horse = example.0b10;  // ERROR no field named `0b10`
```
{==+==}
```rust,compile_fail
let example = ("dog", "cat", "horse");
let dog = example.0;
let cat = example.1;
// 下面的例子是无效的。
let cat = example.01;  // ERROR 字段名不能为 `01`
let horse = example.0b10;  // ERROR 字段名不能为 `0b10`
```
{==+==}


{==+==}
> **Note**: Tuple indices may include certain suffixes, but this is not intended to be valid, and may be removed in a future version.
> See <https://github.com/rust-lang/rust/issues/60210> for more information.
{==+==}
> **注意**: 元组索引可能包括某些后缀，但这并不意味着是有效的，在未来的版本中可能会删除。
> 更多内容见 <https://github.com/rust-lang/rust/issues/60210> 。
{==+==}


{==+==}
#### Floating-point literals
{==+==}
#### 浮点字面值
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> FLOAT_LITERAL :\
> &nbsp;&nbsp; &nbsp;&nbsp; DEC_LITERAL `.`
>   _(not immediately followed by `.`, `_` or an XID_Start character)_\
> &nbsp;&nbsp; | DEC_LITERAL `.` DEC_LITERAL SUFFIX_NO_E<sup>?</sup>\
> &nbsp;&nbsp; | DEC_LITERAL (`.` DEC_LITERAL)<sup>?</sup> FLOAT_EXPONENT SUFFIX<sup>?</sup>\
>
> FLOAT_EXPONENT :\
> &nbsp;&nbsp; (`e`|`E`) (`+`|`-`)<sup>?</sup>
>               (DEC_DIGIT|`_`)<sup>\*</sup> DEC_DIGIT (DEC_DIGIT|`_`)<sup>\*</sup>
>
{==+==}

{==+==}


{==+==}
A _floating-point literal_ has one of two forms:
{==+==}
_floating-point literal_ 浮点字面值两种形式之一:
{==+==}


{==+==}
* A _decimal literal_ followed by a period character `U+002E` (`.`). This is
  optionally followed by another decimal literal, with an optional _exponent_.
* A single _decimal literal_ followed by an _exponent_.
{==+==}
* _decimal literal_ "十进制字面值" 后面一个句号字符 `U+002E` (`.`)，随后是可选的另一个十进制的字面值，并有一个可选的 _exponent_ 指数。
* 单一的 _decimal literal_ "十进制字面值" 随后一个 _exponent_ 指数。
{==+==}


{==+==}
Like integer literals, a floating-point literal may be followed by a
suffix, so long as the pre-suffix part does not end with `U+002E` (`.`).
The suffix may not begin with `e` or `E` if the literal does not include an exponent.
See [literal expressions] for the effect of these suffixes.
{==+==}
和整数字面值一样，浮点字面值后面可以有后缀，只要前缀部分不以 `U+002E` (`.`) 结尾。
如果字面值内容不包含指数，后缀不能以 `e` 或 `E` 开头。
关于这些后缀的实现，请参见字面值表达式 [literal expressions] 。
{==+==}


{==+==}
Examples of floating-point literals which are accepted as literal expressions:
{==+==}
字面值表达式允许的的浮点字面值的示例。
{==+==}


{==+==}
```rust
123.0f64;
0.1f64;
0.1f32;
12E+99_f64;
let x: f64 = 2.;
```
{==+==}

{==+==}


{==+==}
This last example is different because it is not possible to use the suffix
syntax with a floating point literal ending in a period. `2.f64` would attempt
to call a method named `f64` on `2`.
{==+==}
最后一个例子是不同的，因为不能对以句点结尾的浮点字面值使用后缀语法。
 `2.f64` 将试图在 `2` 上调用一个名为 `f64` 的方法。
{==+==}


{==+==}
Note that `-1.0`, for example, is analyzed as two tokens: `-` followed by `1.0`.
{==+==}
注意，例如 `-1.0` 被解析为两个符号: `-` 和 `1.0` 。
{==+==}


{==+==}
Examples of floating-point literals which are not accepted as literal expressions:
{==+==}
字面值表达式不允许的浮点字面值示例:
{==+==}


{==+==}
```rust
# #[cfg(FALSE)] {
2.0f80;
2e5f80;
2e5e6;
2.0e5e6;
1.3e10u64;
# }
```
{==+==}

{==+==}


{==+==}
#### Reserved forms similar to number literals
{==+==}
#### 类似于数字字面值的保留形式
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> RESERVED_NUMBER :\
> &nbsp;&nbsp; &nbsp;&nbsp; BIN_LITERAL \[`2`-`9`&ZeroWidthSpace;]\
> &nbsp;&nbsp; | OCT_LITERAL \[`8`-`9`&ZeroWidthSpace;]\
> &nbsp;&nbsp; | ( BIN_LITERAL | OCT_LITERAL | HEX_LITERAL ) `.` \
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; _(not immediately followed by `.`, `_` or an XID_Start character)_\
> &nbsp;&nbsp; | ( BIN_LITERAL | OCT_LITERAL ) (`e`|`E`)\
> &nbsp;&nbsp; | `0b` `_`<sup>\*</sup> _end of input or not BIN_DIGIT_\
> &nbsp;&nbsp; | `0o` `_`<sup>\*</sup> _end of input or not OCT_DIGIT_\
> &nbsp;&nbsp; | `0x` `_`<sup>\*</sup> _end of input or not HEX_DIGIT_\
> &nbsp;&nbsp; | DEC_LITERAL ( . DEC_LITERAL)<sup>?</sup> (`e`|`E`) (`+`|`-`)<sup>?</sup> _end of input or not DEC_DIGIT_
{==+==}

{==+==}


{==+==}
The following lexical forms similar to number literals are _reserved forms_.
Due to the possible ambiguity these raise, they are rejected by the tokenizer instead of being interpreted as separate tokens.
{==+==}
以下类似于数字字面值的词法是 _保留形式_ 。
由于这些形式可能引起歧义，编译器会拒绝它们，而不会解释为独立token。
{==+==}


{==+==}
* An unsuffixed binary or octal literal followed, without intervening whitespace, by a decimal digit out of the range for its radix.
{==+==}
* 一个无后缀的二进制或八进制字面值，中间没有空白，随后是一个超出其小数范围的十进制数字。
{==+==}


{==+==}
* An unsuffixed binary, octal, or hexadecimal literal followed, without intervening whitespace, by a period character (with the same restrictions on what follows the period as for floating-point literals).
{==+==}
* 一个无后缀的二进制、八进制或十六进制字面值，中间没有空白，随后是一个句号(对句号后面的限制与浮点字面值相同)。
{==+==}


{==+==}
* An unsuffixed binary or octal literal followed, without intervening whitespace, by the character `e` or `E`.
{==+==}
* 一个无后缀的二进制或八进制字面值，中间没有空白，随后是字符 `e` 或 `E` 组成。
{==+==}


{==+==}
* Input which begins with one of the radix prefixes but is not a valid binary, octal, or hexadecimal literal (because it contains no digits).
{==+==}
* 以一个小数点前缀开始的输入，但不是一个有效的二进制、八进制或十六进制字面值(因为它不包含数字)。
{==+==}


{==+==}
* Input which has the form of a floating-point literal with no digits in the exponent.
{==+==}
* 具有浮点字面值形式的输入，指数中没有数字。
{==+==}


{==+==}
Examples of reserved forms:
{==+==}
保留形式的示例:
{==+==}


{==+==}
```rust,compile_fail
0b0102;  // this is not `0b010` followed by `2`
0o1279;  // this is not `0o127` followed by `9`
0x80.0;  // this is not `0x80` followed by `.` and `0`
0b101e;  // this is not a suffixed literal, or `0b101` followed by `e`
0b;      // this is not an integer literal, or `0` followed by `b`
0b_;     // this is not an integer literal, or `0` followed by `b_`
2e;      // this is not a floating-point literal, or `2` followed by `e`
2.0e;    // this is not a floating-point literal, or `2.0` followed by `e`
2em;     // this is not a suffixed literal, or `2` followed by `em`
2.0em;   // this is not a suffixed literal, or `2.0` followed by `em`
```
{==+==}
```rust,compile_fail
0b0102;  // 这不是 `0b010` 随后有 `2`
0o1279;  // 这不是 `0o127` 随后有 `9`
0x80.0;  // 这不是 `0x80` 随后有 `.` 和 `0`
0b101e;  // 这不是有后缀的字面值或 `0b101` 随后有 `e`
0b;      // 这不是一个整数字面值或 `0`随后有 `b`
0b_;     // 这不是一个整数字面值或 `0` 随后有 `b_`
2e;      // 这不是一个浮点字面值或 `2` 随后有 `e`
2.0e;    // 这不是一个浮点字面值或 `2.0` 随后有 `e`
2em;     // 这不是有后缀的字面值或 `2` 随后有 `em`
2.0em;   // 这不是有后缀的字面值或 `2.0` 随后有 `em`
```
{==+==}


{==+==}
## Lifetimes and loop labels
{==+==}
## 生命周期和循环标签
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> LIFETIME_TOKEN :\
> &nbsp;&nbsp; &nbsp;&nbsp; `'` [IDENTIFIER_OR_KEYWORD][identifier]\
> &nbsp;&nbsp; | `'_`
>
> LIFETIME_OR_LABEL :\
> &nbsp;&nbsp; &nbsp;&nbsp; `'` [NON_KEYWORD_IDENTIFIER][identifier]
{==+==}

{==+==}


{==+==}
Lifetime parameters and [loop labels] use LIFETIME_OR_LABEL tokens. Any
LIFETIME_TOKEN will be accepted by the lexer, and for example, can be used in
macros.
{==+==}
生命周期参数和 [loop labels] 循环标签使用 LIFETIME_OR_LABEL 标记。词法分析器接受任何 LIFETIME_TOKEN ，比如，可以在宏中使用。
{==+==}


{==+==}
## Punctuation
{==+==}
## 标点符号
{==+==}


{==+==}
Punctuation symbol tokens are listed here for completeness. Their individual
usages and meanings are defined in the linked pages.
{==+==}
为了完整，这里列出了标点符号token。它们各自的用途和含义定义在链接的页面中。
{==+==}


{==+==}
| Symbol | Name        | Usage |
|--------|-------------|-------|
| `+`    | Plus        | [Addition][arith], [Trait Bounds], [Macro Kleene Matcher][macros]
| `-`    | Minus       | [Subtraction][arith], [Negation]
| `*`    | Star        | [Multiplication][arith], [Dereference], [Raw Pointers], [Macro Kleene Matcher][macros], [Use wildcards]
| `/`    | Slash       | [Division][arith]
| `%`    | Percent     | [Remainder][arith]
| `^`    | Caret       | [Bitwise and Logical XOR][arith]
| `!`    | Not         | [Bitwise and Logical NOT][negation], [Macro Calls][macros], [Inner Attributes][attributes], [Never Type], [Negative impls]
| `&`    | And         | [Bitwise and Logical AND][arith], [Borrow], [References], [Reference patterns]
| <code>\|</code> | Or | [Bitwise and Logical OR][arith], [Closures], Patterns in [match], [if let], and [while let]
| `&&`   | AndAnd      | [Lazy AND][lazy-bool], [Borrow], [References], [Reference patterns]
| <code>\|\|</code> | OrOr | [Lazy OR][lazy-bool], [Closures]
| `<<`   | Shl         | [Shift Left][arith], [Nested Generics][generics]
| `>>`   | Shr         | [Shift Right][arith], [Nested Generics][generics]
| `+=`   | PlusEq      | [Addition assignment][compound]
| `-=`   | MinusEq     | [Subtraction assignment][compound]
| `*=`   | StarEq      | [Multiplication assignment][compound]
| `/=`   | SlashEq     | [Division assignment][compound]
| `%=`   | PercentEq   | [Remainder assignment][compound]
| `^=`   | CaretEq     | [Bitwise XOR assignment][compound]
| `&=`   | AndEq       | [Bitwise And assignment][compound]
| <code>\|=</code> | OrEq | [Bitwise Or assignment][compound]
| `<<=`  | ShlEq       | [Shift Left assignment][compound]
| `>>=`  | ShrEq       | [Shift Right assignment][compound], [Nested Generics][generics]
| `=`    | Eq          | [Assignment], [Attributes], Various type definitions
| `==`   | EqEq        | [Equal][comparison]
| `!=`   | Ne          | [Not Equal][comparison]
| `>`    | Gt          | [Greater than][comparison], [Generics], [Paths]
| `<`    | Lt          | [Less than][comparison], [Generics], [Paths]
| `>=`   | Ge          | [Greater than or equal to][comparison], [Generics]
| `<=`   | Le          | [Less than or equal to][comparison]
| `@`    | At          | [Subpattern binding]
| `_`    | Underscore  | [Wildcard patterns], [Inferred types], Unnamed items in [constants], [extern crates], [use declarations], and [destructuring assignment]
| `.`    | Dot         | [Field access][field], [Tuple index]
| `..`   | DotDot      | [Range][range], [Struct expressions], [Patterns], [Range Patterns][rangepat]
| `...`  | DotDotDot   | [Variadic functions][extern], [Range patterns]
| `..=`  | DotDotEq    | [Inclusive Range][range], [Range patterns]
| `,`    | Comma       | Various separators
| `;`    | Semi        | Terminator for various items and statements, [Array types]
| `:`    | Colon       | Various separators
| `::`   | PathSep     | [Path separator][paths]
| `->`   | RArrow      | [Function return type][functions], [Closure return type][closures], [Function pointer type]
| `=>`   | FatArrow    | [Match arms][match], [Macros]
| `#`    | Pound       | [Attributes]
| `$`    | Dollar      | [Macros]
| `?`    | Question    | [Question mark operator][question], [Questionably sized][sized], [Macro Kleene Matcher][macros]
| `~`    | Tilde       | The tilde operator has been unused since before Rust 1.0, but its token may still be used
{==+==}

{==+==}


{==+==}
## Delimiters
{==+==}
## 分割符号
{==+==}


{==+==}
Bracket punctuation is used in various parts of the grammar. An open bracket
must always be paired with a close bracket. Brackets and the tokens within
them are referred to as "token trees" in [macros].  The three types of brackets are:
{==+==}
括号标点符号用在语法的各部分。一个左括号必须总是与一个右括号相配对。括号和其中的token在 [macros] 中被称为 "token trees" 。三种类型的括号是：
{==+==}


{==+==}
| Bracket | Type            |
|---------|-----------------|
| `{` `}` | Curly braces    |
| `[` `]` | Square brackets |
| `(` `)` | Parentheses     |
{==+==}

{==+==}


{==+==}
## Reserved prefixes
{==+==}
## 保留前缀
{==+==}


{==+==}
> **<sup>Lexer 2021+</sup>**\
> RESERVED_TOKEN_DOUBLE_QUOTE : ( IDENTIFIER_OR_KEYWORD <sub>_Except `b` or `r` or `br`_</sub> | `_` ) `"`\
> RESERVED_TOKEN_SINGLE_QUOTE : ( IDENTIFIER_OR_KEYWORD <sub>_Except `b`_</sub> | `_` ) `'`\
> RESERVED_TOKEN_POUND : ( IDENTIFIER_OR_KEYWORD <sub>_Except `r` or `br`_</sub> | `_` ) `#`
{==+==}

{==+==}


{==+==}
Some lexical forms known as _reserved prefixes_ are reserved for future use.
{==+==}
一些被称为 _保留前缀_ 的词法形式被保留下来，供将来使用。
{==+==}


{==+==}
Source input which would otherwise be lexically interpreted as a non-raw identifier (or a keyword or `_`) which is immediately followed by a `#`, `'`, or `"` character (without intervening whitespace) is identified as a reserved prefix.
{==+==}
如果源码输入在词法上被解析为非原始标识符(或关键字或 `_` )，紧随其后的是 `#` 、 `'` 或 `"` 字符(没有中间的空白) ，刚将其标识为保留前缀。
{==+==}


{==+==}
Note that raw identifiers, raw string literals, and raw byte string literals may contain a `#` character but are not interpreted as containing a reserved prefix.
{==+==}
请注意，原始标识符、原始字符串字面值和原始字节字符串字面值可能包含 `#` 字符，但不会被解释为包含保留前缀。
{==+==}


{==+==}
Similarly the `r`, `b`, and `br` prefixes used in raw string literals, byte literals, byte string literals, and raw byte string literals are not interpreted as reserved prefixes.
{==+==}
同样，在原始字符串字面值、字节字面值、字节字符串字面值和原始字节字符串字面值中使用的 `r` 、 `b` 和 `br` 前缀也不被解释为保留前缀。
{==+==}


{==+==}
> **Edition Differences**: Starting with the 2021 edition, reserved prefixes are reported as an error by the lexer (in particular, they cannot be passed to macros).
>
> Before the 2021 edition, reserved prefixes are accepted by the lexer and interpreted as multiple tokens (for example, one token for the identifier or keyword, followed by a `#` token).
>
> Examples accepted in all editions:
{==+==}
> **版次差异**: 从2021版开始，保留前缀会被词法分析器报告为错误 (尤其不能传递给宏)。
>
> 在2021版本之前，保留前缀被词法分析器接受，并被解释为多个token (比如，token为标识符或关键词，后面是 `#` token)。
>
> 所有版次允许的示例:
{==+==}


{==+==}
> ```rust
> macro_rules! lexes {($($_:tt)*) => {}}
> lexes!{a #foo}
> lexes!{continue 'foo}
> lexes!{match "..." {}}
> lexes!{r#let#foo}         // three tokens: r#let # foo
> ```
>
> Examples accepted before the 2021 edition but rejected later:
> ```rust,edition2018
> macro_rules! lexes {($($_:tt)*) => {}}
> lexes!{a#foo}
> lexes!{continue'foo}
> lexes!{match"..." {}}
> ```
{==+==}
> ```rust
> macro_rules! lexes {($($_:tt)*) => {}}
> lexes!{a #foo}
> lexes!{continue 'foo}
> lexes!{match "..." {}}
> lexes!{r#let#foo}         // 三个token: r#let # foo
> ```
>
> 示例在2021版本之前是允许的，之后不允许:
> ```rust,edition2018
> macro_rules! lexes {($($_:tt)*) => {}}
> lexes!{a#foo}
> lexes!{continue'foo}
> lexes!{match"..." {}}
> ```
{==+==}


{==+==}
[Inferred types]: types/inferred.md
[Range patterns]: patterns.md#range-patterns
[Reference patterns]: patterns.md#reference-patterns
[Subpattern binding]: patterns.md#identifier-patterns
[Wildcard patterns]: patterns.md#wildcard-pattern
[arith]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[array types]: types/array.md
[assignment]: expressions/operator-expr.md#assignment-expressions
[attributes]: attributes.md
[borrow]: expressions/operator-expr.md#borrow-operators
[closures]: expressions/closure-expr.md
[comparison]: expressions/operator-expr.md#comparison-operators
[compound]: expressions/operator-expr.md#compound-assignment-expressions
[constants]: items/constant-items.md
[dereference]: expressions/operator-expr.md#the-dereference-operator
[destructuring assignment]: expressions/underscore-expr.md
[extern crates]: items/extern-crates.md
[extern]: items/external-blocks.md
[field]: expressions/field-expr.md
[floating-point types]: types/numeric.md#floating-point-types
[function pointer type]: types/function-pointer.md
[functions]: items/functions.md
[generics]: items/generics.md
[identifier]: identifiers.md
[if let]: expressions/if-expr.md#if-let-expressions
[keywords]: keywords.md
[lazy-bool]: expressions/operator-expr.md#lazy-boolean-operators
[literal expressions]: expressions/literal-expr.md
[loop labels]: expressions/loop-expr.md
[macros]: macros-by-example.md
[match]: expressions/match-expr.md
[negation]: expressions/operator-expr.md#negation-operators
[negative impls]: items/implementations.md
[never type]: types/never.md
[numeric types]: types/numeric.md
[paths]: paths.md
[patterns]: patterns.md
[question]: expressions/operator-expr.md#the-question-mark-operator
[range]: expressions/range-expr.md
[rangepat]: patterns.md#range-patterns
[raw pointers]: types/pointer.md#raw-pointers-const-and-mut
[references]: types/pointer.md
[sized]: trait-bounds.md#sized
[struct expressions]: expressions/struct-expr.md
[trait bounds]: trait-bounds.md
[tuple index]: expressions/tuple-expr.md#tuple-indexing-expressions
[tuple structs]: items/structs.md
[tuple variants]: items/enumerations.md
[tuples]: types/tuple.md
[unary minus operator]: expressions/operator-expr.md#negation-operators
[use declarations]: items/use-declarations.md
[use wildcards]: items/use-declarations.md
[while let]: expressions/loop-expr.md#predicate-pattern-loops
{==+==}

{==+==}