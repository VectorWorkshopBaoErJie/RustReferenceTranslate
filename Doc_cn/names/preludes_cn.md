{==+==}
# Preludes

A *prelude* is a collection of names that are automatically brought into scope
of every module in a crate.

These prelude names are not part of the module itself: they are implicitly
queried during [name resolution]. For example, even though something like
[`Box`] is in scope in every module, you cannot refer to it as `self::Box`
because it is not a member of the current module.

There are several different preludes:

- [Standard library prelude]
- [Extern prelude]
- [Language prelude]
- [`macro_use` prelude]
- [Tool prelude]
{==+==}
# Preludes

 *预定义* 是一组自动引入到一个 crate 中每个模块作用域的名称。

这些预定义名称不是模块本身的一部分: 它们在 [命名解析][name resolution] 期间隐式查询。例如，即使像 [`Box`] 这样的内容在每个模块中都处于作用域中，但您无法将其称为 `self::Box` ，因为它不是当前模块的成员。

有几个不同的预定义：

- [标准库预定义][Standard library prelude]
- [外部预定义][Extern prelude]
- [语言预定义][Language prelude]
- [`macro_use` 预定义][`macro_use` prelude]
- [工具预定义][Tool prelude]
{==+==}


{==+==}
## Standard library prelude

Each crate has a standard library prelude, which consists of the names from a single standard library module.
The module used depends on the crate's edition, and on whether the [`no_std` attribute] is applied to the crate:

Edition | `no_std` not applied        | `no_std` applied
--------| --------------------------- | ----------------------------
2015    | [`std::prelude::rust_2015`] | [`core::prelude::rust_2015`]
2018    | [`std::prelude::rust_2018`] | [`core::prelude::rust_2018`]
2021    | [`std::prelude::rust_2021`] | [`core::prelude::rust_2021`]


> **Note**:
>
> [`std::prelude::rust_2015`] and [`std::prelude::rust_2018`] have the same contents as [`std::prelude::v1`].
>
> [`core::prelude::rust_2015`] and [`core::prelude::rust_2018`] have the same contents as [`core::prelude::v1`].
{==+==}
## 标准库预定义

每个crate都有一个标准库预定义，其中包括来自单个标准库模块的名称。所使用的模块取决于crate的版本，以及是否应用了 [`no_std`属性][`no_std` attribute] ：

版本 | 未应用`no_std` | 应用`no_std`
--------| --------------------------- | ----------------------------
2015    | [`std::prelude::rust_2015`] | [`core::prelude::rust_2015`]
2018    | [`std::prelude::rust_2018`] | [`core::prelude::rust_2018`]
2021    | [`std::prelude::rust_2021`] | [`core::prelude::rust_2021`]

> **注意**：
>
> [`std::prelude::rust_2015`] 和 [`std::prelude::rust_2018`] 的内容与 [`std::prelude::v1`] 相同。
>
> [`core::prelude::rust_2015`] 和 [`core::prelude::rust_2018`] 的内容与 [`core::prelude::v1`] 相同。
{==+==}


{==+==}
## Extern prelude

External crates imported with [`extern crate`] in the root module or provided
to the compiler (as with the `--extern` flag with `rustc`) are added to the
*extern prelude*. If imported with an alias such as `extern crate orig_name as
new_name`, then the symbol `new_name` is instead added to the prelude.

The [`core`] crate is always added to the extern prelude. The [`std`] crate is
added as long as the [`no_std` attribute] is not specified in the crate root.

> **Edition Differences**: In the 2015 edition, crates in the extern prelude
> cannot be referenced via [use declarations], so it is generally standard
> practice to include `extern crate` declarations to bring them into scope.
>
> Beginning in the 2018 edition, [use declarations] can reference crates in
> the extern prelude, so it is considered unidiomatic to use `extern crate`.

> **Note**: Additional crates that ship with `rustc`, such as [`alloc`], and
> [`test`], are not automatically included with the `--extern` flag when using
> Cargo. They must be brought into scope with an `extern crate` declaration,
> even in the 2018 edition.
>
> ```rust
> extern crate alloc;
> use alloc::rc::Rc;
> ```
>
> Cargo does bring in `proc_macro` to the extern prelude for proc-macro crates
> only.

<!--
See https://github.com/rust-lang/rust/issues/57288 for more about the
alloc/test limitation.
-->
{==+==}
## 外部预定义

在根模块中使用 [`extern crate`][`extern crate`] 导入的外部 crate 或提供给编译器的 crate (例如使用`rustc`的`--extern`标志) 将被添加到 *外部定义* 中。如果使用别名导入，例如 `extern crate orig_name as new_name` ，则符号 `new_name` 将添加到预定义中。

[`core`] crate始终会被添加到外部预定义中。只要在crate根中未指定[`no_std`属性]，[`std`] crate也会被添加到预定义中。

> **版本差异**: 在2015版中，外部预定义中的crate不能通过[use声明]引用，因此通常的做法是使用`extern crate`声明将它们引入作用域。
>
> 从2018年版开始，[use声明]可以引用外部预定义中的crate，因此使用`extern crate`被认为不符合惯例。

> **注意**: 随 `rustc` 一起提供的其他 crate ，例如 [`alloc`] 和 [`test`] ，在使用 Cargo 时不会自动包含在 `--extern` 标志中。它们必须通过 `extern crate` 声明引入作用域，即使在2018年版中也是如此。
>
> ```rust
> extern crate alloc;
> use alloc::rc::Rc;
> ```
>
> 对于 proc-macro crate ，Cargo会将 `proc_macro` 自动添加到外部预定义中。

<!--
有关alloc/test限制的更多信息，请参见https://github.com/rust-lang/rust/issues/57288。
-->
{==+==}


{==+==}
### The `no_std` attribute

By default, the standard library is automatically included in the crate root
module. The [`std`] crate is added to the root, along with an implicit
[`macro_use` attribute] pulling in all macros exported from `std` into the
[`macro_use` prelude]. Both [`core`] and [`std`] are added to the [extern
prelude].

The *`no_std` [attribute]* may be applied at the crate level to prevent the
[`std`] crate from being automatically added into scope. It does three things:

* Prevents `std` from being added to the [extern prelude](#extern-prelude).
* Affects which module is used to make up the [standard library prelude] (as described above).
* Injects the [`core`] crate into the crate root instead of [`std`], and pulls
  in all macros exported from `core` in the [`macro_use` prelude].

> **Note**: Using the core prelude over the standard prelude is useful when
> either the crate is targeting a platform that does not support the standard
> library or is purposefully not using the capabilities of the standard
> library. Those capabilities are mainly dynamic memory allocation (e.g. `Box`
> and `Vec`) and file and network capabilities (e.g. `std::fs` and `std::io`).
{==+==}
### `no_std` 属性

默认情况下，标准库会自动包含在 crate 的根模块中。[`std`] crate 与一个隐式的 [`macro_use` 属性][`macro_use` attribute] 一同添加到根模块，将 `std` 导出的所有宏都添加到 [`macro_use` 预定义模块][`macro_use` prelude] 中。同时，[`core`] 和 [`std`] 也会添加到 [extern prelude] 中。

可以在 crate 级别应用 *`no_std` [属性][attribute]* 来阻止自动将 [`std`] crate 添加到作用域中。它会做三件事情：

* 防止 `std` 添加到 [extern prelude] 中。
* 影响用于构建 [standard library prelude] 的模块 (如上所述) 。
* 将 [`core`] crate 注入到 crate 根模块中，而不是 [`std`]，并将所有从 `core` 导出的宏添加到 [`macro_use` 预定义模块][`macro_use` prelude] 中。

> **注意**: 当 crate 目标平台不支持标准库或有意不使用标准库的功能时，使用核心预定义 (core prelude) 而不是标准预定义 (standard prelude) 很有用。
> 这些功能主要包括动态内存分配 (例如 `Box` 和 `Vec` ) 以及文件和网络功能 (例如 `std::fs` 和 `std::io` ) 。
{==+==}


{==+==}
<div class="warning">

Warning: Using `no_std` does not prevent the standard library from being
linked in. It is still valid to put `extern crate std;` into the crate and
dependencies can also link it in.

</div>

## Language prelude

The language prelude includes names of types and attributes that are built-in
to the language. The language prelude is always in scope. It includes the following:

* [Type namespace]
    * [Boolean type] — `bool`
    * [Textual types] — `char` and `str`
    * [Integer types] — `i8`, `i16`, `i32`, `i64`, `i128`, `u8`, `u16`, `u32`, `u64`, `u128`
    * [Machine-dependent integer types] — `usize` and `isize`
    * [floating-point types] — `f32` and `f64`
* [Macro namespace]
    * [Built-in attributes]
{==+==}
<div class="warning">

警告: 使用 `no_std` 不会防止标准库被链接。在 crate 中放置 `extern crate std;` 是有效的，依赖项也可以链接它。

</div>

## 语言预定义

语言预定义包括内置于语言中的类型和属性名称。语言预定义始终在作用域内。它包括以下内容：

* [类型命名空间][Type namespace]
    * [布尔类型][Boolean type] — `bool`
    * [文本类型][Textual types] — `char` 和 `str`
    * [整数类型][Integer types] — `i8`、`i16`、`i32`、`i64`、`i128`、`u8`、`u16`、`u32`、`u64`、`u128`
    * [与机器相关的整数类型][Machine-dependent integer types] — `usize` 和 `isize`
    * [浮点数类型][floating-point types] — `f32` 和 `f64`
* [宏命名空间][Macro namespace]
    * [内置属性][Built-in attributes]
{==+==}


{==+==}
## `macro_use` prelude

The `macro_use` prelude includes macros from external crates that were
imported by the [`macro_use` attribute] applied to an [`extern crate`].

## Tool prelude

The tool prelude includes tool names for external tools in the [type
namespace]. See the [tool attributes] section for more details.

## The `no_implicit_prelude` attribute

The *`no_implicit_prelude` [attribute]* may be applied at the crate level or
on a module to indicate that it should not automatically bring the [standard
library prelude], [extern prelude], or [tool prelude] into scope for that
module or any of its descendants.

This attribute does not affect the [language prelude].

> **Edition Differences**: In the 2015 edition, the `no_implicit_prelude`
> attribute does not affect the [`macro_use` prelude], and all macros exported
> from the standard library are still included in the `macro_use` prelude.
> Starting in the 2018 edition, it will remove the `macro_use` prelude.
{==+==}
## `macro_use` 预定义

`macro_use` 预定义包括来自外部 crate 的宏，这些宏是通过 [`macro_use`] 属性定义的。

## 工具预定义

工具预定义包括外部工具的工具名称，位于 [类型命名空间][type namespace] 中。有关详细信息，请参见 [工具属性][tool attributes] 部分。

## `no_implicit_prelude` 属性

*`no_implicit_prelude` [attribute]* 可以应用于 crate 级别或模块上，表示它不应自动为该模块或其任何子级引入 [标准库预定义][standard library prelude]、 [extern 预定义][extern prelude] 或 [工具预定义][tool prelude] 。

该属性不影响 [语言预定义][language prelude] 。

> **版本差异**：在 2015 版中，`no_implicit_prelude` 属性不影响 [`macro_use` 预定义][`macro_use` prelude] ，并且标准库导出的所有宏仍包含在 `macro_use` 预定义中。从 2018 版开始，将删除 `macro_use` 预定义。
{==+==}


{==+==}
[`alloc`]: ../../alloc/index.html
[`Box`]: ../../std/boxed/struct.Box.html
[`core::prelude::v1`]: ../../core/prelude/v1/index.html
[`core::prelude::rust_2015`]: ../../core/prelude/rust_2015/index.html
[`core::prelude::rust_2018`]: ../../core/prelude/rust_2018/index.html
[`core::prelude::rust_2021`]: ../../core/prelude/rust_2021/index.html
[`core`]: ../../core/index.html
[`extern crate`]: ../items/extern-crates.md
[`macro_use` attribute]: ../macros-by-example.md#the-macro_use-attribute
[`macro_use` prelude]: #macro_use-prelude
[`no_std` attribute]: #the-no_std-attribute
[`no_std` attribute]: #the-no_std-attribute
[`std::prelude::v1`]: ../../std/prelude/v1/index.html
[`std::prelude::rust_2015`]: ../../std/prelude/rust_2015/index.html
[`std::prelude::rust_2018`]: ../../std/prelude/rust_2018/index.html
[`std::prelude::rust_2021`]: ../../std/prelude/rust_2021/index.html
[`std`]: ../../std/index.html
[`test`]: ../../test/index.html
[attribute]: ../attributes.md
[Boolean type]: ../types/boolean.md
[Built-in attributes]: ../attributes.md#built-in-attributes-index
[extern prelude]: #extern-prelude
[floating-point types]: ../types/numeric.md#floating-point-types
[Integer types]: ../types/numeric.md#integer-types
[Language prelude]: #language-prelude
[Machine-dependent integer types]: ../types/numeric.md#machine-dependent-integer-types
[Macro namespace]: namespaces.md
[name resolution]: name-resolution.md
[Standard library prelude]: #standard-library-prelude
[Textual types]: ../types/textual.md
[tool attributes]: ../attributes.md#tool-attributes
[Tool prelude]: #tool-prelude
[Type namespace]: namespaces.md
[use declarations]: ../items/use-declarations.md
{==+==}

{==+==}