{==+==}
# External blocks
{==+==}
# 外部块
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ExternBlock_ :\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> `extern` [_Abi_]<sup>?</sup> `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _ExternalItem_<sup>\*</sup>\
> &nbsp;&nbsp; `}`
>
> _ExternalItem_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_MacroInvocationSemi_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | ( [_Visibility_]<sup>?</sup> ( [_StaticItem_] | [_Function_] ) )\
> &nbsp;&nbsp; )
{==+==}

{==+==}


{==+==}
External blocks provide _declarations_ of items that are not _defined_ in the
current crate and are the basis of Rust's foreign function interface. These are
akin to unchecked imports.
{==+==}
外部块提供了在当前crate中未定义条目的 _声明_ ，是Rust外部函数接口的主要部分。
类似于未检查的导入。
{==+==}


{==+==}
Two kinds of item _declarations_ are allowed in external blocks: [functions] and
[statics]. Calling functions or accessing statics that are declared in external
blocks is only allowed in an `unsafe` context.
{==+==}
在外部块中允许两种条目 _声明_ : [functions] 和 [statics] 。
只有在 `unsafe` 的上下中，才允许调用外部块中声明的函数或访问静态变量。
{==+==}


{==+==}
The `unsafe` keyword is syntactically allowed to appear before the `extern`
keyword, but it is rejected at a semantic level. This allows macros to consume
the syntax and make use of the `unsafe` keyword, before removing it from the
token stream.
{==+==}
语法上允许 `unsafe` 关键字出现在 `extern` 关键字之前，但在语义层面上排斥。
这允许宏消耗并使用 `unsafe` 关键字，之后从 token 流中删除。
{==+==}


{==+==}
## Functions
{==+==}
## 函数
{==+==}


{==+==}
Functions within external blocks are declared in the same way as other Rust
functions, with the exception that they must not have a body and are instead
terminated by a semicolon. Patterns are not allowed in parameters, only
[IDENTIFIER] or `_` may be used. Function qualifiers (`const`, `async`,
`unsafe`, and `extern`) are not allowed.
{==+==}
外部块中的函数与其他Rust函数的声明方式相同，但是不能有函数体，而以分号结束。
参数中不允许使用模式，只能使用 [IDENTIFIER] 或 `_` 。
不允许使用函数限定符 ( `const` , `async` , `unsafe` , 和 `extern` ) 。
{==+==}


{==+==}
Functions within external blocks may be called by Rust code, just like
functions defined in Rust. The Rust compiler automatically translates between
the Rust ABI and the foreign ABI.
{==+==}
Rust代码可以调用外部块中的函数，就像在Rust中定义的函数一样。
Rust编译器会自动在Rust ABI和外部ABI之间进行翻译。
{==+==}


{==+==}
A function declared in an extern block is implicitly `unsafe`. When coerced to
a function pointer, a function declared in an extern block has type `unsafe
extern "abi" for<'l1, ..., 'lm> fn(A1, ..., An) -> R`, where `'l1`, ... `'lm`
are its lifetime parameters, `A1`, ..., `An` are the declared types of its
parameters and `R` is the declared return type.
{==+==}
在外部块中声明的函数隐含 `unsafe` 。
当持有到函数指针时，在外部块中声明的函数的类型是 `unsafe extern "abi" for<'l1, ..., 'lm> fn(A1, ..., An) -> R` ，
其中 `'l1` , ... `'lm` 是其生命周期参数，`A1` , ... , `An` 是其参数的声明类型， `R` 是声明的返回类型。
{==+==}


{==+==}
## Statics
{==+==}

{==+==}


{==+==}
Statics within external blocks are declared in the same way as [statics] outside of external blocks,
except that they do not have an expression initializing their value.
It is `unsafe` to access a static item declared in an extern block, whether or
not it's mutable, because there is nothing guaranteeing that the bit pattern at the static's
memory is valid for the type it is declared with, since some arbitrary (e.g. C) code is in charge
of initializing the static.
{==+==}
外部块中的静态的声明方式与外部块外的 [statics] 相同，只是没有初始化表达式。
访问在外部块中声明的静态是 `unsafe` 的，无论它是否是可变的，因为无法保证静态内存的位模式对它所声明的类型有效，比如一些不确定的 (例如C) 代码对静态初始化。
{==+==}


{==+==}
Extern statics can be either immutable or mutable just like [statics] outside of external blocks.
An immutable static *must* be initialized before any Rust code is executed. It is not enough for
the static to be initialized before Rust code reads from it.
{==+==}
外部静态可以是不可变的，也可以是可变的，就像外部块外的 [statics] 一样。
一个不可变的静态必须在任何Rust代码执行之前被初始化。仅仅在Rust代码读取之前初始化静态是不够的。
{==+==}


{==+==}
## ABI
{==+==}

{==+==}


{==+==}
By default external blocks assume that the library they are calling uses the
standard C ABI on the specific platform. Other ABIs may be specified using an
`abi` string, as shown here:
{==+==}
默认情况下，外部区假定它们所调用的库使用特定平台上的标准C ABI。
其他ABI可以使用 `abi` 字符串来指定，如下所示:
{==+==}


{==+==}
```rust
// Interface to the Windows API
extern "stdcall" { }
```
{==+==}
```rust
// 对于Windows API的接口
extern "stdcall" { }
```
{==+==}


{==+==}
There are three ABI strings which are cross-platform, and which all compilers
are guaranteed to support:
{==+==}
有三种ABI字符串是跨平台的，而且所有的编译器都保证支持它们:
{==+==}


{==+==}
* `extern "Rust"` -- The default ABI when you write a normal `fn foo()` in any
  Rust code.
* `extern "C"` -- This is the same as `extern fn foo()`; whatever the default
  your C compiler supports.
* `extern "system"` -- Usually the same as `extern "C"`, except on Win32, in
  which case it's `"stdcall"`, or what you should use to link to the Windows
  API itself
{==+==}
* `extern "Rust"` -- 在Rust代码中书写的普通 `fn foo()` 时，默认的ABI。
* `extern "C"` -- 这与 `extern fn foo()` 相同；不管你的C编译器支持什么默认值。
* `extern "system"` -- 通常与 `extern "C"` 相同，除了在Win32上，这时，它是 `"stdcall"` ，你可以用它来链接Windows API。
{==+==}


{==+==}
There are also some platform-specific ABI strings:
{==+==}
一些特定平台的ABI字符串:
{==+==}


{==+==}
* `extern "cdecl"` -- The default for x86\_32 C code.
* `extern "stdcall"` -- The default for the Win32 API on x86\_32.
* `extern "win64"` -- The default for C code on x86\_64 Windows.
* `extern "sysv64"` -- The default for C code on non-Windows x86\_64.
* `extern "aapcs"` -- The default for ARM.
* `extern "fastcall"` -- The `fastcall` ABI -- corresponds to MSVC's
  `__fastcall` and GCC and clang's `__attribute__((fastcall))`
* `extern "vectorcall"` -- The `vectorcall` ABI -- corresponds to MSVC's
  `__vectorcall` and clang's `__attribute__((vectorcall))`
{==+==}
* `extern "cdecl"` -- x86\_32 C代码的默认值。
* `extern "stdcall"` -- x86/32 上的 Win32 API 的默认值。
* `extern "win64"` -- 在 x86/64 Windows 上，C代码的默认值。
* `extern "sysv64"` -- 在非 Windows x86/64 上的C代码的默认值。
* `extern "aapcs"` -- ARM的默认值。
* `extern "fastcall"` -- `fastcall` ABI -- 类似于MSVC的 `__fastcall` 和GCC及clang的 `__attribute__((fastcall))` 。
* `extern "vectorcall"` -- `vectorcall` ABI -- 类似于MSVC的 `__vectorcall` 和clang的 `__attribute__((vectorcall))` 。
{==+==}


{==+==}
## Variadic functions
{==+==}
## 可变函数
{==+==}


{==+==}
Functions within external blocks may be variadic by specifying `...` as the
last argument. There must be at least one parameter before the variadic
parameter. The variadic parameter may optionally be specified with an
identifier.
{==+==}
外部块中的函数可以通过指定 `...` 作为最后一个参数而成为可变的。
在可变参数之前必须至少有一个参数。变量参数可以选择指定一个标识符。
{==+==}


{==+==}
```rust
extern "C" {
    fn foo(x: i32, ...);
    fn with_name(format: *const u8, args: ...);
}
```
{==+==}

{==+==}


{==+==}
## Attributes on extern blocks
{==+==}
## 外部块的属性
{==+==}


{==+==}
The following [attributes] control the behavior of external blocks.
{==+==}
以下 [attributes] 控制外部块的行为。
{==+==}


{==+==}
### The `link` attribute
{==+==}
### `link` 属性
{==+==}


{==+==}
The *`link` attribute* specifies the name of a native library that the
compiler should link with for the items within an `extern` block. It uses the
[_MetaListNameValueStr_] syntax to specify its inputs. The `name` key is the
name of the native library to link. The `kind` key is an optional value which
specifies the kind of library with the following possible values:
{==+==}
 *`link` 属性* 指定了一个本地库的名称，编译器应该将 `extern` 块中的条目与之链接。
它使用 [_MetaListNameValueStr_] 语法来指定其输入。
 `name` 键是要链接的本地库的名称。 `kind` 键是一个可选的值，用于指定库的种类，可选值如下:
{==+==}


{==+==}
- `dylib` — Indicates a dynamic library. This is the default if `kind` is not
  specified.
- `static` — Indicates a static library.
- `framework` — Indicates a macOS framework. This is only valid for macOS
  targets.
- `raw-dylib` — Indicates a dynamic library where the compiler will generate
  an import library to link against (see [`dylib` versus `raw-dylib`] below
  for details). This is only valid for Windows targets.
{==+==}
- `dylib` — 表示为动态库。如果没有指定 `kind` ，这是默认。
- `static` — 表示为静态库。
- `framework` — 表示为macOS框架。这只对macOS目标有效。
- `raw-dylib` — 表示为动态库，编译器将生成一个导入库来进行链接 (详见下文[ `dylib` 与 `raw-dylib` ])。这只对Windows目标有效。
{==+==}


{==+==}
The `name` key must be included if `kind` is specified.
{==+==}
如果指定了 `kind` ，必须包括 `name` 键。
{==+==}


{==+==}
The optional `modifiers` argument is a way to specify linking modifiers for the
library to link.
Modifiers are specified as a comma-delimited string with each modifier prefixed
with either a `+` or `-` to indicate that the modifier is enabled or disabled,
respectively.
Specifying multiple `modifiers` arguments in a single `link` attribute,
or multiple identical modifiers in the same `modifiers` argument is not currently supported. \
Example: `#[link(name = "mylib", kind = "static", modifiers = "+whole-archive")`.
{==+==}

{==+==}


{==+==}
The `wasm_import_module` key may be used to specify the [WebAssembly module]
name for the items within an `extern` block when importing symbols from the
host environment. The default module name is `env` if `wasm_import_module` is
not specified.
{==+==}

{==+==}


{==+==}
<!-- ignore: requires extern linking -->
```rust,ignore
#[link(name = "crypto")]
extern {
    // …
}

#[link(name = "CoreFoundation", kind = "framework")]
extern {
    // …
}

#[link(wasm_import_module = "foo")]
extern {
    // …
}
```
{==+==}

{==+==}


{==+==}
It is valid to add the `link` attribute on an empty extern block. You can use
this to satisfy the linking requirements of extern blocks elsewhere in your
code (including upstream crates) instead of adding the attribute to each extern
block.
{==+==}

{==+==}


{==+==}
#### Linking modifiers: `bundle`
{==+==}

{==+==}


{==+==}
This modifier is only compatible with the `static` linking kind.
Using any other kind will result in a compiler error.
{==+==}

{==+==}


{==+==}
When building a rlib or staticlib `+bundle` means that the native static library
will be packed into the rlib or staticlib archive, and then retrieved from there
during linking of the final binary.
{==+==}

{==+==}


{==+==}
When building a rlib `-bundle` means that the native static library is registered as a dependency
of that rlib "by name", and object files from it are included only during linking of the final
binary, the file search by that name is also performed during final linking. \
When building a staticlib `-bundle` means that the native static library is simply not included
into the archive and some higher level build system will need to add it later during linking of
the final binary.
{==+==}

{==+==}


{==+==}
This modifier has no effect when building other targets like executables or dynamic libraries.
{==+==}

{==+==}


{==+==}
The default for this modifier is `+bundle`.
{==+==}

{==+==}


{==+==}
More implementation details about this modifier can be found in
[`bundle` documentation for rustc].
{==+==}

{==+==}


{==+==}
#### Linking modifiers: `whole-archive`
{==+==}

{==+==}


{==+==}
This modifier is only compatible with the `static` linking kind.
Using any other kind will result in a compiler error.
{==+==}

{==+==}


{==+==}
`+whole-archive` means that the static library is linked as a whole archive
without throwing any object files away.
{==+==}

{==+==}


{==+==}
The default for this modifier is `-whole-archive`.
{==+==}

{==+==}


{==+==}
More implementation details about this modifier can be found in
[`whole-archive` documentation for rustc].
{==+==}

{==+==}


{==+==}
### Linking modifiers: `verbatim`
{==+==}

{==+==}


{==+==}
This modifier is compatible with all linking kinds.
{==+==}

{==+==}


{==+==}
`+verbatim` means that rustc itself won't add any target-specified library prefixes or suffixes
(like `lib` or `.a`) to the library name, and will try its best to ask for the same thing from the
linker.
{==+==}

{==+==}


{==+==}
`-verbatim` means that rustc will either add a target-specific prefix and suffix to the library
name before passing it to linker, or won't prevent linker from implicitly adding it.
{==+==}

{==+==}


{==+==}
The default for this modifier is `-verbatim`.
{==+==}

{==+==}


{==+==}
More implementation details about this modifier can be found in
[`verbatim` documentation for rustc].
{==+==}

{==+==}


{==+==}
#### `dylib` versus `raw-dylib`
{==+==}

{==+==}


{==+==}
On Windows, linking against a dynamic library requires that an import library
is provided to the linker: this is a special static library that declares all
of the symbols exported by the dynamic library in such a way that the linker
knows that they have to be dynamically loaded at runtime.
{==+==}

{==+==}


{==+==}
Specifying `kind = "dylib"` instructs the Rust compiler to link an import
library based on the `name` key. The linker will then use its normal library
resolution logic to find that import library. Alternatively, specifying
`kind = "raw-dylib"` instructs the compiler to generate an import library
during compilation and provide that to the linker instead.
{==+==}

{==+==}


{==+==}
`raw-dylib` is only supported on Windows and not supported on 32-bit x86
(`target_arch="x86"`). Using it when targeting other platforms or
x86 on Windows will result in a compiler error.
{==+==}

{==+==}


{==+==}
### The `link_name` attribute
{==+==}

{==+==}


{==+==}
The *`link_name` attribute* may be specified on declarations inside an `extern`
block to indicate the symbol to import for the given function or static. It
uses the [_MetaNameValueStr_] syntax to specify the name of the symbol.
{==+==}

{==+==}


{==+==}
```rust
extern {
    #[link_name = "actual_symbol_name"]
    fn name_in_rust();
}
```
{==+==}

{==+==}


{==+==}
Using this attribute with the `link_ordinal` attribute will result in a
compiler error.
{==+==}

{==+==}


{==+==}
### The `link_ordinal` attribute
{==+==}

{==+==}


{==+==}
The *`link_ordinal` attribute* can be applied on declarations inside an `extern`
block to indicate the numeric ordinal to use when generating the import library
to link against. An ordinal is a unique number per symbol exported by a dynamic
library on Windows and can be used when the library is being loaded to find
that symbol rather than having to look it up by name.
{==+==}

{==+==}


{==+==}
<div class="warning">
{==+==}

{==+==}


{==+==}
Warning: `link_ordinal` should only be used in cases where the ordinal of the
symbol is known to be stable: if the ordinal of a symbol is not explicitly set
when its containing binary is built then one will be automatically assigned to
it, and that assigned ordinal may change between builds of the binary.
{==+==}

{==+==}


{==+==}
</div>
{==+==}

{==+==}


{==+==}
<!-- ignore: Only works on x86 Windows -->
```rust,ignore
#[link(name = "exporter", kind = "raw-dylib")]
extern "stdcall" {
    #[link_ordinal(15)]
    fn imported_function_stdcall(i: i32);
}
```
{==+==}

{==+==}


{==+==}
This attribute is only used with the `raw-dylib` linking kind.
Using any other kind will result in a compiler error.
{==+==}

{==+==}


{==+==}
Using this attribute with the `link_name` attribute will result in a
compiler error.
{==+==}

{==+==}


{==+==}
### Attributes on function parameters
{==+==}

{==+==}


{==+==}
Attributes on extern function parameters follow the same rules and
restrictions as [regular function parameters].
{==+==}

{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[WebAssembly module]: https://webassembly.github.io/spec/core/syntax/modules.html
[functions]: functions.md
[statics]: static-items.md
[_Abi_]: functions.md
[_Function_]: functions.md
[_InnerAttribute_]: ../attributes.md
[_MacroInvocationSemi_]: ../macros.md#macro-invocation
[_MetaListNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[_MetaNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[_OuterAttribute_]: ../attributes.md
[_StaticItem_]: static-items.md
[_Visibility_]: ../visibility-and-privacy.md
[attributes]: ../attributes.md
[regular function parameters]: functions.md#attributes-on-function-parameters
[`bundle` documentation for rustc]: ../../rustc/command-line-arguments.html#linking-modifiers-bundle
[`whole-archive` documentation for rustc]: ../../rustc/command-line-arguments.html#linking-modifiers-whole-archive
[`verbatim` documentation for rustc]: ../../rustc/command-line-arguments.html#linking-modifiers-verbatim
[`dylib` versus `raw-dylib`]: #dylib-versus-raw-dylib
{==+==}

{==+==}