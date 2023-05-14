{==+==}
## Procedural Macros
{==+==}
## 过程宏
{==+==}


{==+==}
*Procedural macros* allow creating syntax extensions as execution of a function.
Procedural macros come in one of three flavors:
{==+==}
*过程宏* 是以可执行函数的形式创建语法扩展。
过程宏有三种添加形式:
{==+==}


{==+==}
* [Function-like macros] - `custom!(...)`
* [Derive macros] - `#[derive(CustomDerive)]`
* [Attribute macros] - `#[CustomAttribute]`
{==+==}
* [函数宏] - `custom!(...)` 
* [衍生宏] - `#[derive(CustomDerive)]` 
* [属性宏] - `#[CustomAttribute]` 
{==+==}


{==+==}
Procedural macros allow you to run code at compile time that operates over Rust
syntax, both consuming and producing Rust syntax. You can sort of think of
procedural macros as functions from an AST to another AST.
{==+==}
过程宏是在编译时运行代码，对 Rust 语法进行操作，输入语法，而产生新的语法。
你可以把过程宏想象成，把一段代码片段转换为另一段代码片段的函数。
{==+==}


{==+==}
Procedural macros must be defined in a crate with the [crate type] of
`proc-macro`.
{==+==}
过程宏必须定义在 [crate 类型][crate type] 为 `proc-macro` 的 crate 中。
{==+==}


{==+==}
> **Note**: When using Cargo, Procedural macro crates are defined with the
> `proc-macro` key in your manifest:
>
{==+==}
> **注意**: 在使用 Cargo 时，可用 crate 配置清单中的 `proc-macro` 键来定义。
>
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
这个特殊的函数，要么返回语法，要么恐慌，要么死循环。
返回语法时，至于是替换语法，还是增加语法，取决于过程宏的类型。
编译器能够捕获过程宏的恐慌，并变成编译器错误。
编译器无法捕获死循环，将导致编译器挂起。
{==+==}


{==+==}
Procedural macros run during compilation, and thus have the same resources that
the compiler has. For example, standard input, error, and output are the same
that the compiler has access to. Similarly, file access is the same. Because
of this, procedural macros have the same security concerns that [Cargo's
build scripts] have.
{==+==}
过程宏在编译过程中运行，类似于编译器的脚本，因而拥有与编译器相同的资源。
例如，标准输入输出及错误与编译器所能访问的相同。
同样地，文件系统的访问也是一样的。
因而，过程宏与 [Cargo 构建脚本][Cargo's build scripts] 有同样的安全问题。
{==+==}


{==+==}
Procedural macros have two ways of reporting errors. The first is to panic. The
second is to emit a [`compile_error`] macro invocation.
{==+==}
过程宏有两种报告错误的方式。一是恐慌。
二是触发 [`compile_error`] 宏调用。
{==+==}


{==+==}
### The `proc_macro` crate
{==+==}
### `proc_macro` crate
{==+==}


{==+==}
Procedural macro crates almost always will link to the compiler-provided
[`proc_macro` crate]. The `proc_macro` crate provides types required for
writing procedural macros and facilities to make it easier.
{==+==}
过程宏 crate 往往需要链接编译器提供的 [`proc_macro` crate] ，其提供了编写过程宏所需的相关类型和功能，使编写过程宏更加容易。
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
该 crate 包含的主要类型为 [`TokenStream`] 。
从而编写过程宏时可以操作 *token 流* ，而不是 AST 节点，这更加便捷和稳定。
 *token 流* 大致相当于 `Vec<TokenTree>` ，其中 `TokenTree` 可以大致认为是词法 Token 。
例如 `foo` 是 `Ident` Token， `.` 是 `Punct` Token，而 `1.2` 是 `Literal` Token。
与 `Vec<TokenTree>` 不同，`TokenStream` 类型的克隆更轻便。
{==+==}


{==+==}
All tokens have an associated `Span`. A `Span` is an opaque value that cannot
be modified but can be manufactured. `Span`s represent an extent of source
code within a program and are primarily used for error reporting. While you
cannot modify a `Span` itself, you can always change the `Span` *associated*
with any token, such as through getting a `Span` from another token.
{==+==}
所有 Token 都有一个相关联的 `Span` 值，该值是不透明的，不能修改，但可以创建。
该值表示源码中一个作用域，作用主要是用于错误报告。
虽然不能直接修改，但 Token 可以改变与之 *关联* 的 `Span` ，比如从另一个 Token 获得一个 `Span` 。
{==+==}


{==+==}
### Procedural macro hygiene
{==+==}
### 过程宏卫生性
{==+==}


{==+==}
Procedural macros are *unhygienic*. This means they behave as if the output
token stream was simply written inline to the code it's next to. This means that
it's affected by external items and also affects external imports.
{==+==}
过程宏是 *unhygienic* "非卫生" 的，行为简单，像是直接把输出 Token 流写入到了其围绕的代码中一样。
意味着，会受到外部条目的影响，也会影响到外部导入部分。
{==+==}


{==+==}
Macro authors need to be careful to ensure their macros work in as many contexts
as possible given this limitation. This often includes using absolute paths to
items in libraries (for example, `::std::option::Option` instead of `Option`) or
by ensuring that generated functions have names that are unlikely to clash with
other functions (like `__internal_foo` instead of `foo`).
{==+==}
因为有这个限制，宏作者需要非常小心，以保证宏能在更多情况下工作。
通常需要: 使用库中条目的绝对路径 (例如 `::std::option::Option` 而不是 `Option` ) ，
或者确保生成的函数名称不太可能与其他函数冲突 (例如 `__internal_foo` 而不是 `foo` ) 。
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
*函数式过程宏* 使用宏调用操作符 (`!`) 来调用。
{==+==}


{==+==}
These macros are defined by a [public]&#32;[function] with the `proc_macro`
[attribute] and a signature of `(TokenStream) -> TokenStream`. The input
[`TokenStream`] is what is inside the delimiters of the macro invocation and the
output [`TokenStream`] replaces the entire macro invocation.
{==+==}
这些宏是由一个具有 `proc_macro` [属性][attribute] 和 `(TokenStream) -> TokenStream` 函数签名的 [pub][public]&#32;[函数][function] 定义的。
函数输入 [`TokenStream`] 是宏调用定界符号内的内容，函数输出 [`TokenStream`] 是整个宏调用的内容。
{==+==}


{==+==}
For example, the following macro definition ignores its input and outputs a
function `answer` into its scope.
{==+==}
例如，下面的定义的过程宏，忽略了输入内容，然后将一个 `answer` 函数输出到了所处作用域。
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
然后可在 crate 中使用该宏后，打印 "42" 到标准输出。
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
函数式过程宏可以处在任意宏调用位置，其中包括 [语句][statements] 、 [表达式][expressions] 、 [模式][patterns] 、 [类型表达式][type expressions] 、 [条目][item] ，
包括 [`extern` 块][`extern` blocks] 中的条目，内部及trait [实现][implementations] ，以及 [trait 定义][trait definitions] 。
{==+==}


{==+==}
### Derive macros
{==+==}
### 衍生宏
{==+==}


{==+==}
*Derive macros* define new inputs for the [`derive` attribute]. These macros
can create new [items] given the token stream of a [struct], [enum], or [union].
They can also define [derive macro helper attributes].
{==+==}
*衍生宏* 为 [`derive` 属性][`derive` attribute] 定义了新的输入项。
可以为 [结构体][struct] 、 [枚举][enum] 或 [联合体][union] 创建新的 [条目][items] 。
也可以定义 [衍生宏辅助属性][derive macro helper attributes] 。
{==+==}


{==+==}
Custom derive macros are defined by a [public]&#32;[function] with the
`proc_macro_derive` attribute and a signature of `(TokenStream) -> TokenStream`.
{==+==}
衍生宏可在 [pub][public]&#32;[函数][function] 附加 `proc_macro_derive` 属性来定义，函数签名为 `(TokenStream) -> TokenStream` 。
{==+==}


{==+==}
The input [`TokenStream`] is the token stream of the item that has the `derive`
attribute on it. The output [`TokenStream`] must be a set of items that are
then appended to the [module] or [block] that the item from the input
[`TokenStream`] is in.
{==+==}
函数输入 [`TokenStream`] 是具有 `derive` 属性条目的 token 流。
函数输出 [`TokenStream`] 必须是一组条目，然后添加到输入 [`TokenStream`] 的条目所在的 [模块][module] 或 [块][block] 中。
{==+==}


{==+==}
The following is an example of a derive macro. Instead of doing anything
useful with its input, it just appends a function `answer`.
{==+==}
下面是一个衍生宏的例子，该宏没有处理输入，只是添加了一个函数 `answer` 。
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
然后使用其衍生宏:
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
#### 衍生宏辅助属性
{==+==}


{==+==}
Derive macros can add additional [attributes] into the scope of the [item]
they are on. Said attributes are called *derive macro helper attributes*. These
attributes are [inert], and their only purpose is to be fed into the derive
macro that defined them. That said, they can be seen by all macros.
{==+==}
衍生宏可以在其所在的 [条目][item] 的作用域内添加额外的 [属性][attributes] 。
这些属性被称为 *衍生宏辅助属性* 。
这些属性是 [惰性的][inert] ，其唯一目的是嵌入到定义它们的衍生宏中。
也就是说，让所有的宏可以看到这些属性。
{==+==}


{==+==}
The way to define helper attributes is to put an `attributes` key in the
`proc_macro_derive` macro with a comma separated list of identifiers that are
the names of the helper attributes.
{==+==}
通过 `proc_macro_derive` 宏内的 `attributes` 键定义辅助属性，用逗号分隔的标识符列表，即辅助属性的名称。
{==+==}


{==+==}
For example, the following derive macro defines a helper attribute
`helper`, but ultimately doesn't do anything with it.
{==+==}
例如，下面衍生宏定义了一个辅助属性 `helper` ，但对于属性并没有做什么事情。
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
然后在结构体上使用衍生宏:
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
*属性宏* 定义了新的 [外围属性][attributes] ，可以附加到 [条目][items] 上，
包括 [`extern` 块][`extern` blocks] 中的条目，内部及trait 的 [实现][implementations] ，以及 [trait 定义][trait definitions] 。
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
属性宏是由附加 `proc_macro_attribute` [属性][attribute] 的 [pub][public]&#32;[函数][function] 来定义的，其函数签名为 `(TokenStream, TokenStream) -> TokenStream` 。
第一个 [`TokenStream`] 是属性名称后定界符号中的 token 树，不包括外部定界符号。
如果属性仅为名称，则该 [`TokenStream`] 为空。
第二个 [`TokenStream`] 是 [条目][item] 的其他部分，包括 [条目][item] 上的其他 [属性][attributes] 。
返回的 [`TokenStream`] 用任意数量的 [条目][item] 替换原 [条目][item] 。
{==+==}


{==+==}
For example, this attribute macro takes the input stream and returns it as is,
effectively being the no-op of attributes.
{==+==}
例如，下面的属性宏接收输入流后按原样返回，实际上是一个空操作的属性。
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
下面这个例子展示了，通过属性宏得到的字符串化的 [`TokenStream`s] 。
运行后，其输出内容展示在以 "out:" 为前缀的函数后的注释中。
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
### 声明宏 Token 和过程宏 Token 
{==+==}


{==+==}
Declarative `macro_rules` macros and procedural macros use similar, but
different definitions for tokens (or rather [`TokenTree`s].)
{==+==}
声明式 `macro_rules` 宏和过程宏使用的 Token 规格类似，但也有所不同。
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
在 `macro_rules` 中的 Token 树 (对应于 `tt` 匹配器) 被定义为
- 定界符号的组 (`(...)`, `{...}`, 等) 。
- 语言支持的所有运算符，包括单字符和多字符运算符 (`+`, `+=`) 。
    - 请注意，这个集合不包括单引号 `'` 。
- 字面值 (`"string"`, `1`, 等) 。
    - 请注意，负 (如 `-1`) 并不是这类字面值 Token 的一部分，而是单独的 Token 。
{==+==}


{==+==}
- Identifiers, including keywords (`ident`, `r#ident`, `fn`)
- Lifetimes (`'ident`)
- Metavariable substitutions in `macro_rules` (e.g. `$my_expr` in
  `macro_rules! mac { ($my_expr: expr) => { $my_expr } }` after the `mac`'s
  expansion, which will be considered a single token tree regardless of the
  passed expression)
{==+==}
- 标识符，包括关键字 (`ident`, `r#ident`, `fn`)
- 生命周期 ( `'ident` )
- 在 `macro_rules` 中元变量将替换 (如 `$my_expr` 在 `macro_rules! mac { ($my_expr: expr) => { $my_expr } }` 在 `mac` 的展开之后，无论传递的表达式是什么，都会被认为是单一的 Token 树 )
{==+==}


{==+==}
Token trees in procedural macros are defined as
- Delimited groups (`(...)`, `{...}`, etc)
- All punctuation characters used in operators supported by the language (`+`,
  but not `+=`), and also the single quote `'` character (typically used in
  lifetimes, see below for lifetime splitting and joining behavior)
{==+==}
过程宏中的 Token 树被定义为
- 定界符号的组 (`(...)`, `{...}`, 等)
- 语言支持的运算符中使用的所有标点符号 (`+` ，但不是 `+=`)，还有单引号 `'` 字符 (通常用在生命周期上，关于生命周期的分割和连接行为见下文)
{==+==}


{==+==}
- Literals (`"string"`, `1`, etc)
    - Negation (e.g. `-1`) is supported as a part of integer
      and floating point literals.
- Identifiers, including keywords (`ident`, `r#ident`, `fn`)
{==+==}
- 字面值 (`"string"`, `1`, 等)
    - 支持负 (如 `-1` ) 作为整数和浮点数字面值的一部分。
- 标识符，包括关键字 (`ident`, `r#ident`, `fn`)
{==+==}


{==+==}
Mismatches between these two definitions are accounted for when token streams
are passed to and from procedural macros. \
Note that the conversions below may happen lazily, so they might not happen if
the tokens are not actually inspected.
{==+==}
当 Token 流被传递到过程宏时，会考虑这两个定义之间不匹配的情况。
请注意，下面的转换可能会产生惰性，所以如果没有实际检验 Token ，就可能不会发生。
{==+==}


{==+==}
When passed to a proc-macro
- All multi-character operators are broken into single characters.
- Lifetimes are broken into a `'` character and an identifier.
{==+==}
传递给过程宏时
- 所有的多字符运算符都分解成单字符。
- 生命周期分解成一个 `'` 字符和一个标识符。
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
- 所有元变量的替换都表示为它们实际的 Token 流。
    - 当需要保留语法分析的优先级时，这样的 Token 流可以被包装成带有隐式定界符号 ([Delimiter::None]) 的定界组 ([Group]) 。
    - `tt` 和 `ident` 的替换从来不会被包装在这样的组中，而总是作为它们的实际的 Token 树来表示。
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
当从过程宏触发时
- 在适用的情况下，标点符号被粘接在多字符运算符中。
- 与标识符连接的单引号 `'` 被粘接在生命周期中。
- 负字面值被转换为两个标记 ( `-` 和字面值 )，当需要保留语法分析的优先级时，可能会被包裹在一个带定界符号的组 ([`Group`]) 中，并带有隐式定界符号 ([`Delimiter::None`]) 。
{==+==}


{==+==}
Note that neither declarative nor procedural macros support doc comment tokens
(e.g. `/// Doc`), so they are always converted to token streams representing
their equivalent `#[doc = r"str"]` attributes when passed to macros.
{==+==}
请注意，无论是声明性的还是过程性的宏都不支持 doc 注释标记 (例如 `/// Doc` ) ，当其传递给宏时，总是转换为表示其等价的 `#[doc = r"str"]` 属性的 Token 流。
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