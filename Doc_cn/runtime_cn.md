{==+==}
# The Rust runtime

This section documents features that define some aspects of the Rust runtime.

## The `panic_handler` attribute

The *`panic_handler` attribute* can only be applied to a function with signature
`fn(&PanicInfo) -> !`. The function marked with this [attribute] defines the behavior of panics. The
[`PanicInfo`] struct contains information about the location of the panic. There must be a single
`panic_handler` function in the dependency graph of a binary, dylib or cdylib crate.

Below is shown a `panic_handler` function that logs the panic message and then halts the
thread.
{==+==}
# Rust 运行时

本节记录了定义 Rust 运行时某些方面的功能。

## `panic_handler` 属性

*`panic_handler` 属性* 只能应用于具有签名 `fn(&PanicInfo) -> !` 的函数。使用此 [属性][attribute] 标记的函数定义了 panic 的行为。
[`PanicInfo`] 结构包含有关 panic 位置的信息。在二进制、 dylib 或 cdylib crate 的依赖图中，必须有一个单独的 `panic_handler` 函数。

下面是一个 `panic_handler` 函数，它记录 panic 消息，然后停止线程。
{==+==}


{==+==}
<!-- ignore: test infrastructure can't handle no_std -->
```rust,ignore
#![no_std]

use core::fmt::{self, Write};
use core::panic::PanicInfo;

struct Sink {
    // ..
#    _0: (),
}
#
# impl Sink {
#     fn new() -> Sink { Sink { _0: () }}
# }
#
# impl fmt::Write for Sink {
#     fn write_str(&mut self, _: &str) -> fmt::Result { Ok(()) }
# }

#[panic_handler]
fn panic(info: &PanicInfo) -> ! {
    let mut sink = Sink::new();

    // logs "panicked at '$reason', src/main.rs:27:4" to some `sink`
    let _ = writeln!(sink, "{}", info);

    loop {}
}
```
{==+==}

{==+==}


{==+==}
### Standard behavior

The standard library provides an implementation of `panic_handler` that
defaults to unwinding the stack but that can be [changed to abort the
process][abort]. The standard library's panic behavior can be modified at
runtime with the [set_hook] function.

## The `global_allocator` attribute

The *`global_allocator` attribute* is used on a [static item] implementing the
[`GlobalAlloc`] trait to set the global allocator.

## The `windows_subsystem` attribute

The *`windows_subsystem` attribute* may be applied at the crate level to set
the [subsystem] when linking on a Windows target. It uses the
[_MetaNameValueStr_] syntax to specify the subsystem with a value of either
`console` or `windows`. This attribute is ignored on non-Windows targets, and
for non-`bin` [crate types].

The "console" subsystem is the default. If a console process is run from an
existing console then it will be attached to that console, otherwise a new
console window will be created.

The "windows" subsystem is commonly used by GUI applications that do not want to
display a console window on startup. It will run detached from any existing console.
{==+==}
### 标准行为

标准库提供了一个默认情况下会展开堆栈的 `panic_handler` 实现，但可以 [更改为终止进程][abort]。可以使用 [set_hook] 函数在运行时修改标准库的 panic 行为。

## `global_allocator` 属性

*`global_allocator` 属性* 用于设置实现 [`GlobalAlloc`] trait 的 [静态条目][static item] 的全局分配器。

## `windows_subsystem` 属性

*`windows_subsystem` 属性* 可以在 Windows 目标上的链接时应用于 crate 级别，以设置 [subsystem]。
它使用 [_MetaNameValueStr_] 语法来指定子系统，值为 `console` 或 `windows`。在非 Windows 目标上，此属性将被忽略，对于非 `bin` [crate types] 也是如此。

"console" 子系统是默认值。如果从现有控制台运行控制台进程，则它将附加到该控制台，否则将创建一个新的控制台窗口。

"windows" 子系统通常用于不希望在启动时显示控制台窗口的 GUI 应用程序。它将在任何现有控制台之外运行。
{==+==}


{==+==}
```rust
#![windows_subsystem = "windows"]
```
{==+==}

{==+==}


{==+==}
[_MetaNameValueStr_]: attributes.md#meta-item-attribute-syntax
[`GlobalAlloc`]: ../alloc/alloc/trait.GlobalAlloc.html
[`PanicInfo`]: ../core/panic/struct.PanicInfo.html
[abort]: ../book/ch09-01-unrecoverable-errors-with-panic.html
[attribute]: attributes.md
[crate types]: linkage.md
[set_hook]: ../std/panic/fn.set_hook.html
[static item]: items/static-items.md
[subsystem]: https://msdn.microsoft.com/en-us/library/fcc1zstk.aspx
{==+==}

{==+==}