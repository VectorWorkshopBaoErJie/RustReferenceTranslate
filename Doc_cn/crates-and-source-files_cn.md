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

{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> UTF8BOM : `\uFEFF`\
> SHEBANG : `#!` \~`\n`<sup>\+</sup>[†](#shebang)
{==+==}

{==+==}


{==+==}
> Note: Although Rust, like any other language, can be implemented by an
> interpreter as well as a compiler, the only existing implementation is a
> compiler, and the language has always been designed to be compiled. For these
> reasons, this section assumes a compiler.
{==+==}
> 注意: 尽管Rust和其他语言一样，既可以由解释器实现，也可以由编译器实现，
> 但现有的唯一实现是编译器，而且该语言一直被设计为编译性的。由此原因，本节假设为编译器。
{==+==}


{==+==}
Rust's semantics obey a *phase distinction* between compile-time and
run-time.[^phase-distinction] Semantic rules that have a *static
interpretation* govern the success or failure of compilation, while
semantic rules that have a *dynamic interpretation* govern the behavior of the
program at run-time.
{==+==}
Rust的语义遵守编译时和运行时间的 *phase distinction* 阶段区分。
[^phase-distinction] 具有 *静态解释* 语义规则控制编译的成功或失败， *动态解释* 语义规则管理程序在运行时的行为。
{==+==}


{==+==}
The compilation model centers on artifacts called _crates_. Each compilation
processes a single crate in source form, and if successful, produces a single
crate in binary form: either an executable or some sort of
library.[^cratesourcefile]
{==+==}
编译模型集中于 _crates_ 制品。每次编译都会处理源码形式的crate，如果成功的话，会产生二进制形式的crate: 可执行文件或某种库 。
{==+==}


{==+==}
A _crate_ is a unit of compilation and linking, as well as versioning,
distribution, and runtime loading. A crate contains a _tree_ of nested
[module] scopes. The top level of this tree is a module that is
anonymous (from the point of view of paths within the module) and any item
within a crate has a canonical [module path] denoting its location
within the crate's module tree.
{==+==}
一个 _crate_ 是一个编译和链接的单元，也是版本管理、分发和运行时加载的单元。
一个 crate 包含一个嵌套 [module] 作用域的 _树_ 。这棵树的顶层是一个匿名的模块(从模块内的路径来看) ，crate内的任何条目都有规范的 [module path] ，表示它在crate模块树中的位置。
{==+==}


{==+==}
The Rust compiler is always invoked with a single source file as input, and
always produces a single output crate. The processing of that source file may
result in other source files being loaded as modules. Source files have the
extension `.rs`.
{==+==}
Rust编译器调用总是以单个源文件作为输入，并且总是产生单一的输出crate。
对该源文件的处理可能会导致其他源码文件被加载为模块。源码文件的扩展名为 `.rs` 。
{==+==}


{==+==}
A Rust source file describes a module, the name and location of which &mdash;
in the module tree of the current crate &mdash; are defined from outside the
source file: either by an explicit [_Module_][module] item in a referencing
source file, or by the name of the crate itself. Every source file is a
module, but not every module needs its own source file: [module
definitions][module] can be nested within one file.
{==+==}
一个Rust源码文件描述了一个模块。其名称及位置&mdash; 在当前crate的模块树中&mdash; 由源码文件外部定义: 通过引用源码文件中明确的 [_Module_][module] 条目或者通过crate本身的名称。
每个源码文件都是一个模块，但不是每个模块都需要有自己的源码文件。 [module definitions][module] 模块定义可以嵌套在一个文件中。
{==+==}


{==+==}
Each source file contains a sequence of zero or more [_Item_] definitions, and
may optionally begin with any number of [attributes]
that apply to the containing module, most of which influence the behavior of
the compiler. The anonymous crate module can have additional attributes that
apply to the crate as a whole.
{==+==}
每个源码文件包含一个由零个或多个 [_Item_] 定义组成的序列，并且，以可选的任意数量的 [attributes] 开始，其大部分会影响编译器的行为。
匿名crate模块可以有适用于整个crate的附加属性。
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
// 指定crate名称。
#![crate_name = "projx"]

// 指定输出制品的类型。
#![crate_type = "lib"]

// 开启警告。
// 这可以在任何模块中，而不仅是匿名的crate模块。
#![warn(non_camel_case_types)]
```
{==+==}


{==+==}
## Byte order mark
{==+==}
## 字节指示标识
{==+==}


{==+==}
The optional [_UTF8 byte order mark_] (UTF8BOM production) indicates that the
file is encoded in UTF8. It can only occur at the beginning of the file and
is ignored by the compiler.
{==+==}
可选的 [_UTF8 byte order mark_] (UTF8BOM production) 表示文件是以UTF8编码的。它只能出现在文件的开头，并且被编译器忽略。
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
源码文件可以有一个 [_shebang_] (SHEBANG production) ，它向操作系统表明用什么程序来执行这个文件。
它的作用是把源码文件当作可执行脚本。
执行注解(Shebang)只能出现在文件的开头 (但在可选的 _UTF8BOM_ 之后) 。它被编译器忽略。比如:
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
执行注解语法有一个限制，以避免与 [attribute] 混淆。
`#!` 字符后面不能有 `[` token，忽略中间的 [comments] 注释或 [whitespace] 空白。
如果这个限制失效，那么就不会当成执行注解，而是作为属性的起始。
{==+==}


{==+==}
## Preludes and `no_std`
{==+==}
## Preludes 和 `no_std`
{==+==}


{==+==}
This section has been moved to the [Preludes chapter](names/preludes.md).
<!-- this is to appease the linkchecker, will remove once other books are updated -->
{==+==}
本节已移至 [Preludes chapter](names/preludes.md) 。
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
一个包含 `main` [function] 的crate可以被编译为可执行文件。
 `main` 函数不接受任何参数，不能声明任何 [trait or lifetime bounds] "trait 或生命周期边界"，不能有任何 [where clauses] ，其返回类型必须实现 [`Termination`] trait。
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
 *`no_main` [attribute]* 可以应用在crate层级，以禁止为可执行二进制文件发送 `main` 符号。
当其他被链接的对象定义了 `main` 时，这很有用。
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
 *`crate_name` [attribute]* 可以应用在crate层级，以 [_MetaNameValueStr_] 的语法指定crate的名称。
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
crate名称不能为空，并且只能包含 [Unicode alphanumeric] 或 `_` (U+005F) 字符。
{==+==}


{==+==}
[^phase-distinction]: This distinction would also exist in an interpreter.
    Static checks like syntactic analysis, type checking, and lints should
    happen before the program is executed regardless of when it is executed.
{==+==}
[^phase-distinction]: 这种区别也会存在于解释器中。
    语法分析、类型检查和lints等静态检查应该在程序执行之前进行，而不管它何时被执行。
{==+==}


{==+==}
[^cratesourcefile]: A crate is somewhat analogous to an *assembly* in the
    ECMA-335 CLI model, a *library* in the SML/NJ Compilation Manager, a *unit*
    in the Owens and Flatt module system, or a *configuration* in Mesa.
{==+==}
[^cratesourcefile]: crate 有点类似于ECMA-335 CLI模型中的 *assembly* ， SML/NJ 编译管理器中的 *library* ， Owens 和 Flatt 模块系统中的 *unit* ，或者 Mesa 中的 *configuration* 。
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