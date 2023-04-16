{==+==}
# Crates and source files
{==+==}
# Crate和源码文件
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Crate_ :\
> &nbsp;&nbsp; UTF8BOM<sup>?</sup>\
> &nbsp;&nbsp; SHEBANG<sup>?</sup>\
> &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_Item_]<sup>\*</sup>
{==+==}
> **<sup>语法</sup>**\
> _Crate_ :\
> &nbsp;&nbsp; UTF8BOM<sup>?</sup>\
> &nbsp;&nbsp; 执行注解<sup>?</sup>\
> &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_条目_][_Item_]<sup>\*</sup>
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> UTF8BOM : `\uFEFF`\
> SHEBANG : `#!` \~`\n`<sup>\+</sup>[†](#shebang)
{==+==}
> **<sup>词法</sup>**\
> UTF8BOM : `\uFEFF`\
> 执行注解 : `#!` \~`\n`<sup>\+</sup>[†](#shebang)
{==+==}


{==+==}
> Note: Although Rust, like any other language, can be implemented by an
> interpreter as well as a compiler, the only existing implementation is a
> compiler, and the language has always been designed to be compiled. For these
> reasons, this section assumes a compiler.
{==+==}
> 注意：尽管 Rust 和其他语言一样，可以被解释器实现，但是现有的唯一实现是编译器，
> 并且这门语言一直被设计为编译型语言。因此，本节假设使用的是编译器。
{==+==}


{==+==}
Rust's semantics obey a *phase distinction* between compile-time and
run-time.[^phase-distinction] Semantic rules that have a *static
interpretation* govern the success or failure of compilation, while
semantic rules that have a *dynamic interpretation* govern the behavior of the
program at run-time.
{==+==}
Rust 的语义遵循编译时和运行时之间的阶段区别。
[^phase-distinction] 有一个 *静态解释* 的语义规则来决定编译的成功或失败，而有一个 *动态解释* 的语义规则来决定程序在运行时的行为。
{==+==}


{==+==}
The compilation model centers on artifacts called _crates_. Each compilation
processes a single crate in source form, and if successful, produces a single
crate in binary form: either an executable or some sort of
library.[^cratesourcefile]
{==+==}
编译模型以名为 _crate_ 的构件为中心。
每个编译过程处理单个源代码形式的 crate，如果成功，将生成单个二进制形式的 crate ，生成可执行文件或某种类型的库。[^cratesourcefile]
{==+==}


{==+==}
A _crate_ is a unit of compilation and linking, as well as versioning,
distribution, and runtime loading. A crate contains a _tree_ of nested
[module] scopes. The top level of this tree is a module that is
anonymous (from the point of view of paths within the module) and any item
within a crate has a canonical [module path] denoting its location
within the crate's module tree.
{==+==}
一个 _crate_ 是编译、链接、版本控制、分发和运行时加载的基本单位。
一个 crate 包含了一个嵌套的 [模块][module] 作用域 _树_ 。
这个树的最高层是一个匿名模块，任何 crate 中的条目都有一个规范的 [模块路径][module path] 来表示它在 crate 的模块树中的位置。
{==+==}


{==+==}
The Rust compiler is always invoked with a single source file as input, and
always produces a single output crate. The processing of that source file may
result in other source files being loaded as modules. Source files have the
extension `.rs`.
{==+==}
Rust 编译器总是使用单个源文件作为输入，并始终产生单个输出 crate 。
处理该源文件可能会导致其他源文件作为模块被加载。源文件的扩展名为`.rs`。
{==+==}


{==+==}
A Rust source file describes a module, the name and location of which &mdash;
in the module tree of the current crate &mdash; are defined from outside the
source file: either by an explicit [_Module_][module] item in a referencing
source file, or by the name of the crate itself. Every source file is a
module, but not every module needs its own source file: [module
definitions][module] can be nested within one file.
{==+==}
一个 Rust 的源文件描述了一个模块，它的名称和位置是在源文件之外定义的，即通过引用源文件中的 [Module][module] 条目，或者通过 crate 自身的名称。
每个源文件都是一个模块，但不是每个模块都需要自己的源文件：[模块定义][module] 可以在一个文件中嵌套。
{==+==}


{==+==}
Each source file contains a sequence of zero or more [_Item_] definitions, and
may optionally begin with any number of [attributes]
that apply to the containing module, most of which influence the behavior of
the compiler. The anonymous crate module can have additional attributes that
apply to the crate as a whole.
{==+==}
每个源文件包含零个或多个 [_条目_][_Item_] 定义，可以选择地以任意数量的 [属性][attributes] 开头，这些属性适用于包含的模块，大多数属性都会影响编译器的行为。
匿名的 crate 模块可以有适用于整个 crate 的附加属性。
{==+==}


{==+==}
```rust
// Specify the crate name.
#![crate_name = "projx"]

// Specify the type of output artifact.
#![crate_type = "lib"]

// Turn on a warning.
// This can be done in any module, not just the anonymous crate module.
#![warn(non_camel_case_types)]
```
{==+==}
```rust
// 指定 crate 名称。
#![crate_name = "projx"]

// 指定输出制品的类型。
#![crate_type = "lib"]

// 开启警告。
// 这可以在任何模块中，而不仅是匿名的 crate 模块。
#![warn(non_camel_case_types)]
```
{==+==}


{==+==}
## Byte order mark
{==+==}
## 字节顺序标记
{==+==}


{==+==}
The optional [_UTF8 byte order mark_] (UTF8BOM production) indicates that the
file is encoded in UTF8. It can only occur at the beginning of the file and
is ignored by the compiler.
{==+==}
可选的 [UTF8 字节顺序标记][_UTF8 byte order mark_] (UTF8BOM 产生式) 表示该文件使用 UTF8 编码。它只能出现在文件开头，并且被编译器忽略。
{==+==}


{==+==}
## Shebang
{==+==}
## 执行注解
{==+==}


{==+==}
A source file can have a [_shebang_] (SHEBANG production), which indicates
to the operating system what program to use to execute this file. It serves
essentially to treat the source file as an executable script. The shebang
can only occur at the beginning of the file (but after the optional
_UTF8BOM_). It is ignored by the compiler. For example:
{==+==}
源码文件可以包含 [_shebang_] (执行注解) ，它指示操作系统要使用哪个程序来执行此文件。
它基本上将源文件视为可执行脚本。执行注解只能出现在文件开头 (但在可选的 _UTF8BOM_ 之后) 。
它会被编译器忽略。例如:
{==+==}


{==+==}
<!-- ignore: tests don't like shebang -->
```rust,ignore
#!/usr/bin/env rustx

fn main() {
    println!("Hello!");
}
```
{==+==}

{==+==}


{==+==}
A restriction is imposed on the shebang syntax to avoid confusion with an
[attribute]. The `#!` characters must not be followed by a `[` token, ignoring
intervening [comments] or [whitespace]. If this restriction fails, then it is
not treated as a shebang, but instead as the start of an attribute.
{==+==}
对于执行注解语法，存在限制以避免与 [属性][attribute] 混淆。
 `#!` 字符后面不能跟着 `[` 标记，忽略其中的注释或 [空白][whitespace] 。
 如果此限制失败，则它不会被视为执行注解，而是被视为属性的开始。
{==+==}


{==+==}
## Preludes and `no_std`
{==+==}
## 预定义 和 `no_std`
{==+==}


{==+==}
This section has been moved to the [Preludes chapter](names/preludes.md).
<!-- this is to appease the linkchecker, will remove once other books are updated -->
{==+==}
本节已移至 [预定义章节](names/preludes.md) 。
<!-- this is to appease the linkchecker, will remove once other books are updated -->
{==+==}


{==+==}
## Main Functions
{==+==}
## Main 函数
{==+==}


{==+==}
A crate that contains a `main` [function] can be compiled to an executable. If a
`main` function is present, it must take no arguments, must not declare any
[trait or lifetime bounds], must not have any [where clauses], and its return
type must implement the [`Termination`] trait.
{==+==}
如果一个 crate 包含一个 `main` [函数][function]，它可以被编译成可执行文件。
如果存在一个 `main` 函数，则它不应接受任何参数，不应声明任何 [trait 或生命周期约束][trait or lifetime bounds]，也不应该有任何 `where` 子句，而且它的返回类型必须实现 [`Termination`] trait。
{==+==}


{==+==}
```rust
fn main() {}
```
```rust
fn main() -> ! {
    std::process::exit(0);
}
```
```rust
fn main() -> impl std::process::Termination {
    std::process::ExitCode::SUCCESS
}
```
{==+==}

{==+==}


{==+==}
> **Note**: Types with implementations of [`Termination`] in the standard library include:
>
> * `()`
> * [`!`]
> * [`Infallible`]
> * [`ExitCode`]
> * `Result<T, E> where T: Termination, E: Debug`
{==+==}
> **注意**: 标准库中具有 [`Termination`] 实现的类型包括:
>
> * `()`
> * [`!`]
> * [`Infallible`]
> * [`ExitCode`]
> * `Result<T, E> where T: Termination, E: Debug`
{==+==}


{==+==}
<!-- If the previous section needs updating (from "must take no arguments"
  onwards, also update it in the testing.md file -->
{==+==}

{==+==}


{==+==}
### The `no_main` attribute
{==+==}
### `no_main` 属性
{==+==}


{==+==}
The *`no_main` [attribute]* may be applied at the crate level to disable
emitting the `main` symbol for an executable binary. This is useful when some
other object being linked to defines `main`.
{==+==}
`no_main` 属性可以应用于 crate 级别，用于禁用可执行二进制文件的 `main` 符号的输出。
这在被链接到某个定义了 `main` 的其他对象时非常有用。
{==+==}


{==+==}
## The `crate_name` attribute
{==+==}
## `crate_name` 属性
{==+==}


{==+==}
The *`crate_name` [attribute]* may be applied at the crate level to specify the
name of the crate with the [_MetaNameValueStr_] syntax.
{==+==}
`crate_name` 属性可以应用于 crate 层级，使用 [_元名称值字符串_][_MetaNameValueStr_] 语法指定 crate 名称。
{==+==}


{==+==}
```rust
#![crate_name = "mycrate"]
```
{==+==}

{==+==}


{==+==}
The crate name must not be empty, and must only contain [Unicode alphanumeric]
or `_` (U+005F) characters.
{==+==}
crate 的名称不能为空，只能包含 [Unicode 字母数字][Unicode alphanumeric] 或 `_` (U+005F) 字符。
{==+==}


{==+==}
[^phase-distinction]: This distinction would also exist in an interpreter.
    Static checks like syntactic analysis, type checking, and lints should
    happen before the program is executed regardless of when it is executed.
{==+==}
[^phase-distinction]: 在一个解释器中，这种区别也会存在。无论何时执行程序，静态检查，例如语法分析、类型检查和 lints 应该先发生，然后才执行程序。
{==+==}


{==+==}
[^cratesourcefile]: A crate is somewhat analogous to an *assembly* in the
    ECMA-335 CLI model, a *library* in the SML/NJ Compilation Manager, a *unit*
    in the Owens and Flatt module system, or a *configuration* in Mesa.
{==+==}
[^cratesourcefile]: 一个 crate 在某种程度上类似于 ECMA-335 CLI 模型中的 *assembly* ， SML/NJ 编译管理器中的 *library* ，Owens 和 Flatt 模块系统中的 *unit*，或 Mesa 中的 *configuration* 。
{==+==}


{==+==}
[Unicode alphanumeric]: ../std/primitive.char.html#method.is_alphanumeric
[`!`]: types/never.md
[_InnerAttribute_]: attributes.md
[_Item_]: items.md
[_MetaNameValueStr_]: attributes.md#meta-item-attribute-syntax
[_shebang_]: https://en.wikipedia.org/wiki/Shebang_(Unix)
[_utf8 byte order mark_]: https://en.wikipedia.org/wiki/Byte_order_mark#UTF-8
[`ExitCode`]: ../std/process/struct.ExitCode.html
[`Infallible`]: ../std/convert/enum.Infallible.html
[`Termination`]: ../std/process/trait.Termination.html
[attribute]: attributes.md
[attributes]: attributes.md
[comments]: comments.md
[function]: items/functions.md
[module]: items/modules.md
[module path]: paths.md
[trait or lifetime bounds]: trait-bounds.md
[where clauses]: items/generics.md#where-clauses
[whitespace]: whitespace.md
{==+==}

{==+==}


{==+==}
<script>
(function() {
    var fragments = {
        "#preludes-and-no_std": "names/preludes.html",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>
{==+==}

{==+==}