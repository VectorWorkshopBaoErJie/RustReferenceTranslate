{==+==}
# Application Binary Interface (ABI)

This section documents features that affect the ABI of the compiled output of
a crate.

See *[extern functions]* for information on specifying the ABI for exporting
functions. See *[external blocks]* for information on specifying the ABI for
linking external libraries.

## The `used` attribute

The *`used` attribute* can only be applied to [`static` items]. This [attribute] forces the
compiler to keep the variable in the output object file (.o, .rlib, etc. excluding final binaries)
even if the variable is not used, or referenced, by any other item in the crate.
However, the linker is still free to remove such an item.

Below is an example that shows under what conditions the compiler keeps a `static` item in the
output object file.
{==+==}
# 应用程序二进制接口 (ABI)

本节记录了影响创建的编译结果的ABI的功能。

有关指定导出函数的ABI的信息，请参见 *[extern functions]* 。 有关指定链接外部库的ABI的信息，请参见 *[external blocks]* 。

## `used`属性

*`used` 属性* 仅适用于 [`static` 条目][`static` items] 。此 [属性][attribute] 强制编译器将变量保留在输出对象文件 (.o 、 .rlib 等，不包括最终二进制文件) 中，即使该变量没有被 crate 中的任何其他条目使用或引用。但是，链接器仍然可以删除这样的条目。

下面是一个示例，展示了编译器在什么条件下保留一个 `static` 条目在输出对象文件中。
{==+==}


{==+==}
``` rust
// foo.rs

// This is kept because of `#[used]`:
#[used]
static FOO: u32 = 0;

// This is removable because it is unused:
#[allow(dead_code)]
static BAR: u32 = 0;

// This is kept because it is publicly reachable:
pub static BAZ: u32 = 0;

// This is kept because it is referenced by a public, reachable function:
static QUUX: u32 = 0;

pub fn quux() -> &'static u32 {
    &QUUX
}

// This is removable because it is referenced by a private, unused (dead) function:
static CORGE: u32 = 0;

#[allow(dead_code)]
fn corge() -> &'static u32 {
    &CORGE
}
```
{==+==}
``` rust
// foo.rs

// 因为 `#[used]` 而保留:
#[used]
static FOO: u32 = 0;

// 因为未使用而可移除：
#[allow(dead_code)]
static BAR: u32 = 0;

// 因为可以被公开访问而保留：
pub static BAZ: u32 = 0;

// 因为被公开可访问的函数所引用而保留：
static QUUX: u32 = 0;

pub fn quux() -> &'static u32 {
    &QUUX
}

// 因为被私有未使用的函数所引用而可移除：
static CORGE: u32 = 0;

#[allow(dead_code)]
fn corge() -> &'static u32 {
    &CORGE
}
```
{==+==}


{==+==}
``` console
$ rustc -O --emit=obj --crate-type=rlib foo.rs

$ nm -C foo.o
0000000000000000 R foo::BAZ
0000000000000000 r foo::FOO
0000000000000000 R foo::QUUX
0000000000000000 T foo::quux
```
{==+==}

{==+==}


{==+==}
## The `no_mangle` attribute

The *`no_mangle` attribute* may be used on any [item] to disable standard
symbol name mangling. The symbol for the item will be the identifier of the
item's name.

Additionally, the item will be publicly exported from the produced library or
object file, similar to the [`used` attribute](#the-used-attribute).

## The `link_section` attribute

The *`link_section` attribute* specifies the section of the object file that a
[function] or [static]'s content will be placed into. It uses the
[_MetaNameValueStr_] syntax to specify the section name.
{==+==}
## `no_mangle` 属性

*`no_mangle` 属性* 可以用于任何 [item] ，以禁用标准符号名混淆。该条目的符号将是该条目名称的标识符。

此外，该条目将从生成的库或对象文件中公开导出，类似于 [`used` 属性](#the-used-attribute) 。

## `link_section` 属性

*`link_section` 属性* 指定将 [函数][function] 或 [静态][static] 内容放置到的对象文件部分。它使用 [_MetaNameValueStr_] 语法来指定部分名称。
{==+==}


{==+==}
<!-- no_run: don't link. The format of the section name is platform-specific. -->
```rust,no_run
#[no_mangle]
#[link_section = ".example_section"]
pub static VAR1: u32 = 1;
```
{==+==}

{==+==}


{==+==}
## The `export_name` attribute

The *`export_name` attribute* specifies the name of the symbol that will be
exported on a [function] or [static]. It uses the [_MetaNameValueStr_] syntax
to specify the symbol name.

```rust
#[export_name = "exported_symbol_name"]
pub fn name_in_rust() { }
```
{==+==}
## `export_name` 属性

*`export_name` 属性* 指定将在 [函数][function] 或 [静态变量][static] 上导出的符号名称。它使用 [_MetaNameValueStr_] 语法来指定符号名称。

```rust
#[export_name = "exported_symbol_name"]
pub fn name_in_rust() { }
```
{==+==}


{==+==}
[_MetaNameValueStr_]: attributes.md#meta-item-attribute-syntax
[`static` items]: items/static-items.md
[attribute]: attributes.md
[extern functions]: items/functions.md#extern-function-qualifier
[external blocks]: items/external-blocks.md
[function]: items/functions.md
[item]: items.md
[static]: items/static-items.md
{==+==}

{==+==}