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
> 注意：尽管 Rust 和其他语言相似，可以实现解释器，但目前仅实现了编译器，并且设计理念也是编译型语言。
{==+==}


{==+==}
Rust's semantics obey a *phase distinction* between compile-time and
run-time.[^phase-distinction] Semantic rules that have a *static
interpretation* govern the success or failure of compilation, while
semantic rules that have a *dynamic interpretation* govern the behavior of the
program at run-time.
{==+==}
Rust 的语义同样遵循 '编译时' 和 '运行时' 两个可区分阶段。
[^phase-distinction] 编译时的 *静态解释* 规则将决定编译的成功或失败，运行时的 *动态解释* 规则将决定程序运行的行为。
{==+==}


{==+==}
The compilation model centers on artifacts called _crates_. Each compilation
processes a single crate in source form, and if successful, produces a single
crate in binary form: either an executable or some sort of
library.[^cratesourcefile]
{==+==}
编译器进行编译的模型主要围绕 _crate_ 。
每个编译过程处理独立的源码形式的 crate ，如果编译成功，将生成单个二进制 crate : 可执行文件或某种类型的库。[^cratesourcefile]

译注: crate 概念不仅限于源码，编译后的文件也是 crate 的一部分。
{==+==}


{==+==}
A _crate_ is a unit of compilation and linking, as well as versioning,
distribution, and runtime loading. A crate contains a _tree_ of nested
[module] scopes. The top level of this tree is a module that is
anonymous (from the point of view of paths within the module) and any item
within a crate has a canonical [module path] denoting its location
within the crate's module tree.
{==+==}
 _crate_ 是编译、链接、版本控制、分发和运行时加载的基本单位。
 crate 包含了一个嵌套的 [模块][module] _树_ 。
该树的最高层是一个匿名模块。
条目都有一个规范的 [模块路径][module path] 以查找它在的模块树中的位置。
{==+==}


{==+==}
The Rust compiler is always invoked with a single source file as input, and
always produces a single output crate. The processing of that source file may
result in other source files being loaded as modules. Source files have the
extension `.rs`.
{==+==}
Rust 编译器总是使用单个源码文件作为输入，并输出产生单个 crate 。
在编译该源码文件时会加载其他源码文件模块。
源码文件的扩展名为 `.rs` 。
{==+==}


{==+==}
A Rust source file describes a module, the name and location of which &mdash;
in the module tree of the current crate &mdash; are defined from outside the
source file: either by an explicit [_Module_][module] item in a referencing
source file, or by the name of the crate itself. Every source file is a
module, but not every module needs its own source file: [module
definitions][module] can be nested within one file.
{==+==}
一个源码文件就是指一个模块，通过源码文件中引用 [模块][module] 的条目从外部定义了模块的名称。 crate 的名称通过属性或外部定义。
一个模块文件中可以嵌套定义子模块。
{==+==}


{==+==}
Each source file contains a sequence of zero or more [_Item_] definitions, and
may optionally begin with any number of [attributes]
that apply to the containing module, most of which influence the behavior of
the compiler. The anonymous crate module can have additional attributes that
apply to the crate as a whole.
{==+==}
每个源码文件 (即模块) 包含了零个或多个 [_条目_][_Item_] ，对于添加的模块 [属性][attributes] 往往会影响编译器的行为。
顶层模块可以添加应用于 crate 的属性。
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
// 以下是添加到顶层模块的属性

// 指定 crate 名称
#![crate_name = "projx"]

// 指定输出 crate 制品的类型
#![crate_type = "lib"]

// 这个属性可以添加到子模块中
#![warn(non_camel_case_types)] // 开启警告。
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
可选的 [UTF8 字节顺序标记][_UTF8 byte order mark_] (UTF8BOM 产生式) 表明该文件使用 UTF8 编码。
只能出现在源码文件的开头，是给解释器实现的预留，会被编译器忽略。
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
源码文件可以包含 [_执行注解_][_shebang_] ，表明操作系统该使用哪个程序来执行此文件，相当于以脚本方式执行。
执行注解只能出现在文件开头，在可选的 _UTF8BOM_ 之后。
同样是给解释器的预留，会被编译器忽略。
例如:
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
 `#!` 字符忽略 [空白][whitespace] 及注释后，不能紧跟 `[` 标记，否则，会被编译器当成属性。
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
如果要把 crate 编译成可执行文件，需要包含一个 `main` [函数][function] 。
此 `main` 函数，不接受任何参数，不能声明任何 [trait 或生命周期约束][trait or lifetime bounds]，也不能有任何 `where` 从句，且返回类型必须实现 [`Termination`] trait。
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
`no_main` 属性可以应用于 crate 顶层模块，从而可以禁用在编译为可执行二进制文件时 `main` 符号的输出。
从而已经定义了 `main` 函数的 carte 可以被其他 crate 链接。
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
*`crate_name` [属性][attribute]* 可以应用于 crate 顶层模块，用来指定 crate 名称，语法为 [_元名称值字符串_][_MetaNameValueStr_] 。
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
该名称不能为空，只能包含 [Unicode 字母数字][Unicode alphanumeric] 或 `_` (U+005F) 字符。
{==+==}


{==+==}
[^phase-distinction]: This distinction would also exist in an interpreter.
    Static checks like syntactic analysis, type checking, and lints should
    happen before the program is executed regardless of when it is executed.
{==+==}
[^phase-distinction]: 在解释器中，同样有所区分。应该先作静态检查，例如语法分析、类型检查和代码分析，然后执行。
{==+==}


{==+==}
[^cratesourcefile]: A crate is somewhat analogous to an *assembly* in the
    ECMA-335 CLI model, a *library* in the SML/NJ Compilation Manager, a *unit*
    in the Owens and Flatt module system, or a *configuration* in Mesa.
{==+==}
[^cratesourcefile]: crate 编译模型类似于 ECMA-335 CLI 模型中的 *assembly* ， SML/NJ 编译管理器中的 *library* ，Owens 和 Flatt 模块系统中的 *unit*，或 Mesa 中的 *configuration* 。
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