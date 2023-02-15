{==+==}
## Procedural Macros
{==+==}
## 过程宏
{==+==}


{==+==}
*Procedural macros* allow creating syntax extensions as execution of a function.
Procedural macros come in one of three flavors:
{==+==}
*Procedural macros* "过程宏" 允许创建语法扩展，作为函数执行。
过程宏有三种添加形式:
{==+==}


{==+==}
* [Function-like macros] - `custom!(...)`
* [Derive macros] - `#[derive(CustomDerive)]`
* [Attribute macros] - `#[CustomAttribute]`
{==+==}
* [Function-like macros] - `custom!(...)`       "函数宏"
* [Derive macros] - `#[derive(CustomDerive)]`   "派生宏"
* [Attribute macros] - `#[CustomAttribute]`     "属性宏"
{==+==}


{==+==}
Procedural macros allow you to run code at compile time that operates over Rust
syntax, both consuming and producing Rust syntax. You can sort of think of
procedural macros as functions from an AST to another AST.
{==+==}
过程宏可以在编译时运行，操作 Rust 语法，既可以消耗 Rust 语法，也可以产生 Rust 语法。
您可以将过程宏视为从 AST 到另一个 AST 的函数。
{==+==}


{==+==}
Procedural macros must be defined in a crate with the [crate type] of
`proc-macro`.
{==+==}
过程宏必须在具有 [crate type] 为 `proc-macro` 的 crate 中定义。
{==+==}


{==+==}
> **Note**: When using Cargo, Procedural macro crates are defined with the
> `proc-macro` key in your manifest:
>
{==+==}
> **注意**: 使用 Cargo 时，可以通过在配置清单中使用 `proc-macro` 键来定义过程宏 crate：
{==+==}


{==+==}
> ```toml
> [lib]
> proc-macro = true
> ```
{==+==}

{==+==}


{==+==}
As functions, they must either return syntax, panic, or loop endlessly. Returned
syntax either replaces or adds the syntax depending on the kind of procedural
macro. Panics are caught by the compiler and are turned into a compiler error.
Endless loops are not caught by the compiler which hangs the compiler.
{==+==}
过程宏本质上是函数，因此它们必须返回语法、发生恐慌（panic）或无限循环。
返回的语法将根据过程宏的类型替换或添加语法。
编译器会捕获并将恐慌转换为编译错误。
编译器无法捕获无限循环，这会导致编译器挂起。
{==+==}


{==+==}
Procedural macros run during compilation, and thus have the same resources that
the compiler has. For example, standard input, error, and output are the same
that the compiler has access to. Similarly, file access is the same. Because
of this, procedural macros have the same security concerns that [Cargo's
build scripts] have.
{==+==}
过程宏在编译时运行，因此具有编译器具有的相同资源。
例如，标准输入、错误和输出与编译器访问的相同。
同样，文件访问也是一样的。由于这个原因，过程宏存在与 [Cargo 的构建脚本][Cargo's build scripts] 相同的安全问题。
{==+==}


{==+==}
Procedural macros have two ways of reporting errors. The first is to panic. The
second is to emit a [`compile_error`] macro invocation.
{==+==}
过程宏有两种报告错误的方式。第一种是发生 panic。
第二种是发出 [`compile_error`] 宏调用。
{==+==}


{==+==}
### The `proc_macro` crate
{==+==}

{==+==}


{==+==}
Procedural macro crates almost always will link to the compiler-provided
[`proc_macro` crate]. The `proc_macro` crate provides types required for
writing procedural macros and facilities to make it easier.
{==+==}
几乎所有过程宏 crate 都会链接到编译器提供的 [proc_macro] crate。
 `proc_macro` crate 提供了编写过程宏所需的类型和一些便利工具，使其更容易实现。
{==+==}


{==+==}
This crate primarily contains a [`TokenStream`] type. Procedural macros operate
over *token streams* instead of AST nodes, which is a far more stable interface
over time for both the compiler and for procedural macros to target. A
*token stream* is roughly equivalent to `Vec<TokenTree>` where a `TokenTree`
can roughly be thought of as lexical token. For example `foo` is an `Ident`
token, `.` is a `Punct` token, and `1.2` is a `Literal` token. The `TokenStream`
type, unlike `Vec<TokenTree>`, is cheap to clone.
{==+==}
`proc_macro` crate 主要包含一个 [`TokenStream`] 类型。
过程宏基于 token stream 而不是 AST 节点进行操作，这对于编译器和过程宏来说，是一种更稳定的接口。token 流大致相当于 `Vec<TokenTree>`，其中 `TokenTree` 可以被视为词法 token 。
例如，`foo` 是一个 `Ident` token，`.` 是一个 `Punct` token，而 `1.2` 是一个 `Literal` token。与 `Vec<TokenTree>` 不同，`TokenStream` 类型非常轻便，可以进行克隆操作。
{==+==}


{==+==}
All tokens have an associated `Span`. A `Span` is an opaque value that cannot
be modified but can be manufactured. `Span`s represent an extent of source
code within a program and are primarily used for error reporting. While you
cannot modify a `Span` itself, you can always change the `Span` *associated*
with any token, such as through getting a `Span` from another token.
{==+==}
所有标记都有一个关联的 `Span`。
`Span` 是一个不可修改但可以制造的不透明值。
`Span` 代表程序中源代码的范围，主要用于错误报告。
虽然您不能修改 `Span` 本身，但是您始终可以更改与任何标记关联的 `Span` ，例如通过从另一个标记获取 `Span` 。
{==+==}


{==+==}
### Procedural macro hygiene
{==+==}
### 过程宏整洁
{==+==}


{==+==}
Procedural macros are *unhygienic*. This means they behave as if the output
token stream was simply written inline to the code it's next to. This means that
it's affected by external items and also affects external imports.
{==+==}
过程宏是 *unhygienic* "非整洁" 的。这意味着它们的行为就像输出的 token 流是直接内联到它旁边的代码中一样。
这意味着它受到外部条目的影响，同时也会影响外部导入的条目。
{==+==}


{==+==}
Macro authors need to be careful to ensure their macros work in as many contexts
as possible given this limitation. This often includes using absolute paths to
items in libraries (for example, `::std::option::Option` instead of `Option`) or
by ensuring that generated functions have names that are unlikely to clash with
other functions (like `__internal_foo` instead of `foo`).
{==+==}
因此，宏作者需要小心谨慎，确保宏在尽可能多的情况下都能正常工作，这通常包括使用库中条目的绝对路径 (例如，`::std::option::Option` 而不是 `Option` )，
或者确保生成的函数名称不太可能与其他函数冲突 (例如，使用 `__internal_foo` 而不是 `foo` )。
{==+==}


{==+==}
### Function-like procedural macros
{==+==}
### 函数式过程宏
{==+==}


{==+==}
*Function-like procedural macros* are procedural macros that are invoked using
the macro invocation operator (`!`).
{==+==}
*Function-like procedural macros* "函数式过程宏" 是通过宏调用运算符 (`!`) 来调用的过程宏。
{==+==}


{==+==}
These macros are defined by a [public]&#32;[function] with the `proc_macro`
[attribute] and a signature of `(TokenStream) -> TokenStream`. The input
[`TokenStream`] is what is inside the delimiters of the macro invocation and the
output [`TokenStream`] replaces the entire macro invocation.
{==+==}
这些宏由具有 `proc_macro` [attribute] 的 [public]&#32;[function] 公共函数定义，其签名为 `(TokenStream) -> TokenStream`。
输入的 [`TokenStream`] 是宏调用定界符中的内容，而输出的 [`TokenStream`] 替换整个宏调用。
{==+==}


{==+==}
For example, the following macro definition ignores its input and outputs a
function `answer` into its scope.
{==+==}
例如，以下宏定义会忽略其输入并输出一个名为 `answer` 的函数到它所在的作用域中。
{==+==}


{==+==}
<!-- ignore: test doesn't support proc-macro -->
```rust,ignore
# #![crate_type = "proc-macro"]
extern crate proc_macro;
use proc_macro::TokenStream;

#[proc_macro]
pub fn make_answer(_item: TokenStream) -> TokenStream {
    "fn answer() -> u32 { 42 }".parse().unwrap()
}
```
{==+==}

{==+==}


{==+==}
And then we use it in a binary crate to print "42" to standard output.
{==+==}
然后我们在二进制 crate 中使用它来将 "42" 打印到标准输出。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
extern crate proc_macro_examples;
use proc_macro_examples::make_answer;

make_answer!();

fn main() {
    println!("{}", answer());
}
```
{==+==}

{==+==}


{==+==}
Function-like procedural macros may be invoked in any macro invocation
position, which includes [statements], [expressions], [patterns], [type
expressions], [item] positions, including items in [`extern` blocks], inherent
and trait [implementations], and [trait definitions].
{==+==}
函数式过程宏可以在任何宏调用位置进行调用，包括 [statements] 语句、 [expressions] 表达式 、 [patterns] 模式、[type expressions] 类型表达式、 [item] 条目位置，包括 [`extern` blocks] 中的条目、 inherent 和 trait [implementations] 实现，以及 [trait definitions] 。
{==+==}


{==+==}
### Derive macros
{==+==}
### 派生宏
{==+==}


{==+==}
*Derive macros* define new inputs for the [`derive` attribute]. These macros
can create new [items] given the token stream of a [struct], [enum], or [union].
They can also define [derive macro helper attributes].
{==+==}
派生宏 (Derive macros) 为 [`derive` 属性][`derive` attribute] 定义了新的输入。
这些宏可以在给定 [struct] 、 [enum] 或 [union] 的 token 流的基础上创建新的条目。
它们也可以定义 [派生宏辅助属性][derive macro helper attributes] 。
{==+==}


{==+==}
Custom derive macros are defined by a [public]&#32;[function] with the
`proc_macro_derive` attribute and a signature of `(TokenStream) -> TokenStream`.
{==+==}
自定义派生宏通过使用 `proc_macro_derive` 属性定义一个 [public]&#32;[function] 函数，函数签名为 `(TokenStream) -> TokenStream` 。
{==+==}


{==+==}
The input [`TokenStream`] is the token stream of the item that has the `derive`
attribute on it. The output [`TokenStream`] must be a set of items that are
then appended to the [module] or [block] that the item from the input
[`TokenStream`] is in.
{==+==}
输入的 [`TokenStream`] 是具有 `derive` 属性的条目的 Token 流。
输出的 [`TokenStream`] 必须是一组条目，然后将它们附加到输入 [`TokenStream`] 所在的 [module] 或 [block] 中。
{==+==}


{==+==}
The following is an example of a derive macro. Instead of doing anything
useful with its input, it just appends a function `answer`.
{==+==}
以下是一个派生宏的示例。它并没有对输入进行任何有用的操作，只是附加了一个名为 `answer` 的函数。
{==+==}


{==+==}
<!-- ignore: test doesn't support proc-macro -->
```rust,ignore
# #![crate_type = "proc-macro"]
extern crate proc_macro;
use proc_macro::TokenStream;

#[proc_macro_derive(AnswerFn)]
pub fn derive_answer_fn(_item: TokenStream) -> TokenStream {
    "fn answer() -> u32 { 42 }".parse().unwrap()
}
```
{==+==}

{==+==}


{==+==}
And then using said derive macro:
{==+==}
然后使用这个派生宏：
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
extern crate proc_macro_examples;
use proc_macro_examples::AnswerFn;

#[derive(AnswerFn)]
struct Struct;

fn main() {
    assert_eq!(42, answer());
}
```
{==+==}

{==+==}


{==+==}
#### Derive macro helper attributes
{==+==}
#### 派生宏辅助属性
{==+==}


{==+==}
Derive macros can add additional [attributes] into the scope of the [item]
they are on. Said attributes are called *derive macro helper attributes*. These
attributes are [inert], and their only purpose is to be fed into the derive
macro that defined them. That said, they can be seen by all macros.
{==+==}
派生宏可以在它们所在的条目的作用域中添加额外的属性。
这些属性被称为派生宏辅助属性。这些属性是 [inert] 惰性的，
它们的唯一目的是提供给定义它们的派生宏使用。
但是，所有宏都可以看到它们。
{==+==}


{==+==}
The way to define helper attributes is to put an `attributes` key in the
`proc_macro_derive` macro with a comma separated list of identifiers that are
the names of the helper attributes.
{==+==}
定义辅助属性的方法是在 `proc_macro_derive` 宏中使用 `attributes` 键，后面跟着一个逗号分隔的标识符列表，这些标识符是辅助属性的名称。
{==+==}


{==+==}
For example, the following derive macro defines a helper attribute
`helper`, but ultimately doesn't do anything with it.
{==+==}
这个派生宏定义了一个辅助属性 `helper`，但实际上并没有对它进行任何操作。
{==+==}


{==+==}
<!-- ignore: test doesn't support proc-macro -->
```rust,ignore
# #![crate_type="proc-macro"]
# extern crate proc_macro;
# use proc_macro::TokenStream;

#[proc_macro_derive(HelperAttr, attributes(helper))]
pub fn derive_helper_attr(_item: TokenStream) -> TokenStream {
    TokenStream::new()
}
```
{==+==}

{==+==}


{==+==}
And then usage on the derive macro on a struct:
{==+==}
然后在结构体上使用派生宏:
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
#[derive(HelperAttr)]
struct Struct {
    #[helper] field: ()
}
```
{==+==}

{==+==}


{==+==}
### Attribute macros
{==+==}
### 属性宏
{==+==}


{==+==}
*Attribute macros* define new [outer attributes][attributes] which can be
attached to [items], including items in [`extern` blocks], inherent and trait
[implementations], and [trait definitions].
{==+==}
*Attribute macros* 定义新的 [外部属性][attributes]，可以附加到 [items] 条目上，包括 [`extern` blocks] 中的条目，以及 inherent 和 trait [implementations] 实现 和 [trait definitions]。
{==+==}


{==+==}
Attribute macros are defined by a [public]&#32;[function] with the
`proc_macro_attribute` [attribute] that has a signature of `(TokenStream,
TokenStream) -> TokenStream`. The first [`TokenStream`] is the delimited token
tree following the attribute's name, not including the outer delimiters. If
the attribute is written as a bare attribute name, the attribute
[`TokenStream`] is empty. The second [`TokenStream`] is the rest of the [item]
including other [attributes] on the [item]. The returned [`TokenStream`]
replaces the [item] with an arbitrary number of [items].
{==+==}
属性宏通过一个带有 `proc_macro_attribute` 属性的公共函数来定义，该函数的签名为 `(TokenStream, TokenStream) -> TokenStream` 。
第一个 [`TokenStream`] 是紧跟属性名称后的被包含在定界符内的 token 树，不包括外部定界符。
如果属性写为一个裸属性名，那么属性 [`TokenStream`] 是空的。
第二个 [`TokenStream`] 是 [item] 的其余部分，包括其他 [attributes]。
返回的 [`TokenStream`] 用任意数量的 [items] 替换了 [item]。
{==+==}


{==+==}
For example, this attribute macro takes the input stream and returns it as is,
effectively being the no-op of attributes.
{==+==}
例如，这个属性宏获取输入流并原样返回，实际上成为属性的空操作函数。
{==+==}


{==+==}
<!-- ignore: test doesn't support proc-macro -->
```rust,ignore
# #![crate_type = "proc-macro"]
# extern crate proc_macro;
# use proc_macro::TokenStream;

#[proc_macro_attribute]
pub fn return_as_is(_attr: TokenStream, item: TokenStream) -> TokenStream {
    item
}
```
{==+==}

{==+==}


{==+==}
This following example shows the stringified [`TokenStream`s] that the attribute
macros see. The output will show in the output of the compiler. The output is
shown in the comments after the function prefixed with "out:".
{==+==}
下面这个例子展示了属性宏所看到的字符串化的 [`TokenStream`s] 。
输出将会在编译器输出中显示。输出以 "out:" 为前缀，显示在函数后的注释中。
{==+==}


{==+==}
<!-- ignore: test doesn't support proc-macro -->
```rust,ignore
// my-macro/src/lib.rs
# extern crate proc_macro;
# use proc_macro::TokenStream;

#[proc_macro_attribute]
pub fn show_streams(attr: TokenStream, item: TokenStream) -> TokenStream {
    println!("attr: \"{}\"", attr.to_string());
    println!("item: \"{}\"", item.to_string());
    item
}
```
{==+==}

{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
// src/lib.rs
extern crate my_macro;

use my_macro::show_streams;

// Example: Basic function
#[show_streams]
fn invoke1() {}
// out: attr: ""
// out: item: "fn invoke1() { }"

// Example: Attribute with input
#[show_streams(bar)]
fn invoke2() {}
// out: attr: "bar"
// out: item: "fn invoke2() {}"

// Example: Multiple tokens in the input
#[show_streams(multiple => tokens)]
fn invoke3() {}
// out: attr: "multiple => tokens"
// out: item: "fn invoke3() {}"

// Example:
#[show_streams { delimiters }]
fn invoke4() {}
// out: attr: "delimiters"
// out: item: "fn invoke4() {}"
```
{==+==}

{==+==}


{==+==}
### Declarative macro tokens and procedural macro tokens
{==+==}
### 声明宏token和过程宏token
{==+==}


{==+==}
Declarative `macro_rules` macros and procedural macros use similar, but
different definitions for tokens (or rather [`TokenTree`s].)
{==+==}
声明式 `macro_rules` 宏和过程宏使用类似但不同的 token (或者更确切地说是 [`TokenTree`] ) 定义。
{==+==}


{==+==}
Token trees in `macro_rules` (corresponding to `tt` matchers) are defined as
- Delimited groups (`(...)`, `{...}`, etc)
- All operators supported by the language, both single-character and
  multi-character ones (`+`, `+=`).
    - Note that this set doesn't include the single quote `'`.
- Literals (`"string"`, `1`, etc)
    - Note that negation (e.g. `-1`) is never a part of such literal tokens,
      but a separate operator token.
{==+==}
 `macro_rules` 中的 token tree (对应于 tt 模式匹配器) 的定义如下：
- 分隔符组 (`(...)`, `{...}`, 等) 
- 语言支持的所有运算符，包括单字符和多字符运算符 (`+`, `+=`) 。
    - 注意，这个集合不包括单引号 `'` 。
- 字面量 (`"string"`, `1` 等)
    - 注意，负 (如 `-1`) 从不是此类字面值 token 的一部分，而是一个独立的运算符 token。
{==+==}


{==+==}
- Identifiers, including keywords (`ident`, `r#ident`, `fn`)
- Lifetimes (`'ident`)
- Metavariable substitutions in `macro_rules` (e.g. `$my_expr` in
  `macro_rules! mac { ($my_expr: expr) => { $my_expr } }` after the `mac`'s
  expansion, which will be considered a single token tree regardless of the
  passed expression)
{==+==}
- 标识符，包括关键字 (`ident`、`r#ident`、`fn`)
- 生命周期 (`'ident`)
- 在 `macro_rules` 中的元变量替换 (例如，在 `macro_rules! mac { ($my_expr: expr) => { $my_expr } }` 中， `$my_expr` 会在 `mac` 展开后被视为单个 Token Tree，而不管传递的表达式)。
{==+==}


{==+==}
Token trees in procedural macros are defined as
- Delimited groups (`(...)`, `{...}`, etc)
- All punctuation characters used in operators supported by the language (`+`,
  but not `+=`), and also the single quote `'` character (typically used in
  lifetimes, see below for lifetime splitting and joining behavior)
{==+==}
过程宏中的 Token 树定义为
- 分隔符组 (`(...)`, `{...}` 等 )
- 语言中支持的所有操作符中的所有标点字符 (例如 `+`，但不包括 `+=` )，以及单引号 `'` 字符 (通常用于生命周期，请参见下面有关生命周期的拆分和连接行为的说明) 。
{==+==}


{==+==}
- Literals (`"string"`, `1`, etc)
    - Negation (e.g. `-1`) is supported as a part of integer
      and floating point literals.
- Identifiers, including keywords (`ident`, `r#ident`, `fn`)
{==+==}
- 字面值 (如 `"string"`, `1` 等)
    - 负号 (如 `-1` ) 被视为整数和浮点数字面值的一部分。
- 标识符，包括关键字 (`ident`, `r#ident`, `fn`)
{==+==}


{==+==}
Mismatches between these two definitions are accounted for when token streams
are passed to and from procedural macros. \
Note that the conversions below may happen lazily, so they might not happen if
the tokens are not actually inspected.
{==+==}
当传递 Token 流 to 或 from 过程宏时，这两个定义之间的不匹配会被纳入考虑。
请注意，下面的转换可能是惰性的，因此如果实际上没有检查 token ，则可能不会发生这些转换。
{==+==}


{==+==}
When passed to a proc-macro
- All multi-character operators are broken into single characters.
- Lifetimes are broken into a `'` character and an identifier.
{==+==}
传递给过程宏时
- 所有多字符运算符都被分解为单个字符。
- 生命周期被分解为 `'` 字符和一个标识符。
{==+==}


{==+==}
- All metavariable substitutions are represented as their underlying token
  streams.
    - Such token streams may be wrapped into delimited groups ([`Group`]) with
      implicit delimiters ([`Delimiter::None`]) when it's necessary for
      preserving parsing priorities.
    - `tt` and `ident` substitutions are never wrapped into such groups and
      always represented as their underlying token trees.
{==+==}
- 所有的元变量替换都以其基础 Token 流表示。
    - 这种 Token 流可能被封装在有隐式分隔符 ([`Delimiter::None`]) 的分组 ([`Group`]) 中，以维护解析的优先级。
    - `tt` 和 `ident` 替换永远不会被封装在这样的分组中，并始终以其基础 Token Tree 表示。
{==+==}


{==+==}
When emitted from a proc macro
- Punctuation characters are glued into multi-character operators
  when applicable.
- Single quotes `'` joined with identifiers are glued into lifetimes.
- Negative literals are converted into two tokens (the `-` and the literal)
  possibly wrapped into a delimited group ([`Group`]) with implicit delimiters
  ([`Delimiter::None`]) when it's necessary for preserving parsing priorities.
{==+==}
当从过程宏返回时，会发生以下转换：
- 标点符号被粘合成多字符运算符 (如果适用) 。
- 单引号 `'` 与标识符结合成生命周期。
- 负数字面量转换为两个标记 ( `-` 和字面量) ，可能会被包装到带有隐式分隔符 ( [`Delimiter::None`] ) 的分组 ([`Group`]) 中，以维护解析优先级。
{==+==}


{==+==}
Note that neither declarative nor procedural macros support doc comment tokens
(e.g. `/// Doc`), so they are always converted to token streams representing
their equivalent `#[doc = r"str"]` attributes when passed to macros.
{==+==}
请注意，声明式宏和过程式宏都不支持文档注释标记 (例如 `/// Doc` ) ，因此当传递给宏时，它们总是转换为表示其等效的 `#[doc = r"str"]` 属性的 token 流。
{==+==}


{==+==}
[Attribute macros]: #attribute-macros
[Cargo's build scripts]: ../cargo/reference/build-scripts.html
[Derive macros]: #derive-macros
[Function-like macros]: #function-like-procedural-macros
[`Delimiter::None`]: ../proc_macro/enum.Delimiter.html#variant.None
[`Group`]: ../proc_macro/struct.Group.html
[`TokenStream`]: ../proc_macro/struct.TokenStream.html
[`TokenStream`s]: ../proc_macro/struct.TokenStream.html
[`TokenTree`s]: ../proc_macro/enum.TokenTree.html
[`compile_error`]: ../std/macro.compile_error.html
[`derive` attribute]: attributes/derive.md
[`extern` blocks]: items/external-blocks.md
[`macro_rules`]: macros-by-example.md
[`proc_macro` crate]: ../proc_macro/index.html
[attribute]: attributes.md
[attributes]: attributes.md
[block]: expressions/block-expr.md
[crate type]: linkage.md
[derive macro helper attributes]: #derive-macro-helper-attributes
[enum]: items/enumerations.md
[expressions]: expressions.md
[function]: items/functions.md
[implementations]: items/implementations.md
[inert]: attributes.md#active-and-inert-attributes
[item]: items.md
[items]: items.md
[module]: items/modules.md
[patterns]: patterns.md
[public]: visibility-and-privacy.md
[statements]: statements.md
[struct]: items/structs.md
[trait definitions]: items/traits.md
[type expressions]: types.md#type-expressions
[type]: types.md
[union]: items/unions.md
{==+==}

{==+==}