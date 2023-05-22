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
> **<sup>语法</sup>**\
> _外部块_ :\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> `extern` [_Abi_]<sup>?</sup> `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _外部条目_<sup>\*</sup>\
> &nbsp;&nbsp; `}`
>
> _外部条目_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_宏调用语句_][_MacroInvocationSemi_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | ( [_可见性_][_Visibility_]<sup>?</sup> ( [_静态条目_][_StaticItem_] | [_函数_][_Function_] ) )\
> &nbsp;&nbsp; )
{==+==}


{==+==}
External blocks provide _declarations_ of items that are not _defined_ in the
current crate and are the basis of Rust's foreign function interface. These are
akin to unchecked imports.
{==+==}
外部块提供了在当前 crate 中未定义的条目的 _声明_ ，并且是 Rust 实现外部函数接口 (FFI) 的基础。这类似于未经检查的导入。
{==+==}


{==+==}
Two kinds of item _declarations_ are allowed in external blocks: [functions] and
[statics]. Calling functions or accessing statics that are declared in external
blocks is only allowed in an `unsafe` context.
{==+==}
在外部块中允许两种条目声明： [函数][functions] 和 [静态变量][statics] 。
仅在 `unsafe` 上下文中才允许调用外部块中声明的函数或访问静态变量。
{==+==}


{==+==}
The `unsafe` keyword is syntactically allowed to appear before the `extern`
keyword, but it is rejected at a semantic level. This allows macros to consume
the syntax and make use of the `unsafe` keyword, before removing it from the
token stream.
{==+==}
`unsafe` 关键字在 `extern` 关键字之前语法上是被允许的，但是在语义层面上会被拒绝。
因而宏可以从语法上使用 `unsafe` 关键字，然后从令牌流中移除。
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
在外部块中声明的函数与其他 Rust 函数的声明方式相同，但是它们不能有函数体，而是以分号结束。
参数中不允许使用模式，只能使用 [标识符][IDENTIFIER] 或 `_` 。不允许使用函数修饰符 (如 `const` 、 `async` 、 `unsafe` 和 `extern` )。
{==+==}


{==+==}
Functions within external blocks may be called by Rust code, just like
functions defined in Rust. The Rust compiler automatically translates between
the Rust ABI and the foreign ABI.
{==+==}
外部块中的函数可以像 rust 中定义的函数一样被 rust 代码调用。
Rust 编译器会自动在 Rust ABI 和外部 ABI 之间进行转换。
{==+==}


{==+==}
A function declared in an extern block is implicitly `unsafe`. When coerced to
a function pointer, a function declared in an extern block has type `unsafe
extern "abi" for<'l1, ..., 'lm> fn(A1, ..., An) -> R`, where `'l1`, ... `'lm`
are its lifetime parameters, `A1`, ..., `An` are the declared types of its
parameters and `R` is the declared return type.
{==+==}
在外部块中声明的函数隐式地被认为是 `unsafe` 的。
当将其强转为函数指针时，外部块中声明的函数类型为 `unsafe extern "abi" for<'l1, ..., 'lm> fn(A1, ..., An) -> R`，
其中 `'l1` , ... `'lm` 为它的生命周期参数， `A1` , ... , `An` 为它参数的声明类型，而 `R` 为它返回值的声明类型。
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
在外部块中声明的静态变量的方式与 [静态变量][statics] 在外部块之外声明的方式相同，但没有初始化表达式。
访问在外部块中声明的静态条目是非安全的，无论它是否可变，因为无法保证静态内存中的位模式是否有效，因为负责初始化该静态的代码是不确定的 (例如 C 语言) 。
{==+==}


{==+==}
Extern statics can be either immutable or mutable just like [statics] outside of external blocks.
An immutable static *must* be initialized before any Rust code is executed. It is not enough for
the static to be initialized before Rust code reads from it.
{==+==}
就像 [静态变量][statics] 在外部块之外声明一样，外部静态变量可以是不可变的也可以是可变的。
在执行任何 Rust 代码之前， *必须* 先初始化不可变的静态。仅在 Rust 代码读取它之前，才将其初始化是不够的。
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
默认情况下，外部块假设它们正在调用的库在特定平台上使用标准 C ABI。
可以使用 `abi` 字符串指定其他 ABI，如下所示:
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
有三个跨平台的 ABI 字符串，以确保所有编译器支持:
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
* `extern "Rust"` -- 在 Rust 代码中编写普通 `fn foo()` 时的默认 ABI。
* `extern "C"` -- 与 `extern fn foo()` 相同，使用你的 C 编译器支持的默认 ABI。
* `extern "system"` -- 通常与 `extern "C"` 相同，但在 Win32 上是 `"stdcall"` ，在链接到 Windows API 本身时应使用它。
{==+==}


{==+==}
There are also some platform-specific ABI strings:
{==+==}
此外，还有一些特定于平台的 ABI 字符串:
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
* `extern "cdecl"` -- x86_32 C 代码的默认 ABI。
* `extern "stdcall"` -- x86_32 上 Win32 API 的默认 ABI。
* `extern "win64"` -- x86_64 Windows 上 C 代码的默认 ABI。
* `extern "sysv64"` -- 非 Windows x86_64 上 C 代码的默认 ABI。
* `extern "aapcs"` -- ARM 的默认 ABI。
* `extern "fastcall"` -- `fastcall` ABI，对应于 MSVC 的 `__fastcall` 和 GCC 和 clang 的 `__attribute__((fastcall))` 。
* `extern "vectorcall"`-- `vectorcall` ABI，对应于 MSVC 的 `__vectorcall` 和 clang 的 `__attribute__((vectorcall))` 。
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
在外部块中的函数可以通过将 `...` 指定最后一个参数为变参。
在变参之前必须至少有一个参数。可以使用标识符选择性地指定变量参数。
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
以下 [属性][attributes] 控制外部块的行为。
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
*`link` 属性* 指定编译器为 `extern` 块中的条目链接的本地库的名称。
它使用 [_元列表名称值字符串_][_MetaListNameValueStr_] 语法来指定其输入。
`name` 键是要链接的本地库的名称。 `kind` 键是可选值，用于指定以下可能值的库类型:
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
- `dylib` - 表示动态库。如果未指定 `kind` ，则为默认值。
- `static` - 表示静态库。
- `framework` - 表示 macOS 框架。仅适用于 macOS 目标。
- `raw-dylib` - 表示动态库，其中编译器将生成要链接的导入库 (详情参见 [`dylib` 对比 `raw-dylib`][`dylib` versus `raw-dylib`] )。仅适用于 Windows 目标。
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
可选的 `modifiers` 参数是指定要链接的库的链接修饰符的一种方法。
修饰符以逗号分隔的字符串形式指定，每个修饰符前有 `+` 或 `-` 前缀，以表明该修饰符已启用或已禁用。
目前不支持在单个 `link` 属性中指定多个 `modifiers` 参数，或在同一 `modifiers` 参数中指定多个相同的修饰符。
例如: `#[link(name = "mylib", kind = "static", modifiers = "+whole-archive")]` 。 
{==+==}


{==+==}
The `wasm_import_module` key may be used to specify the [WebAssembly module]
name for the items within an `extern` block when importing symbols from the
host environment. The default module name is `env` if `wasm_import_module` is
not specified.
{==+==}
当从主机环境导入符号时，可以使用 `wasm_import_module` 键来指定 `extern` 块中条目的 [WebAssembly 模块][WebAssembly module] 名称。
如果未指定 `wasm_import_module` ，则默认模块名为 `env` 。
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
可以在空的外部块上添加 `link` 属性。
你可以使用它来满足代码中其他地方 (包括上游 crates) 的外部块的链接要求，而不是为每个外部块都添加属性。
{==+==}


{==+==}
#### Linking modifiers: `bundle`
{==+==}
#### 链接修饰符: `bundle`
{==+==}


{==+==}
This modifier is only compatible with the `static` linking kind.
Using any other kind will result in a compiler error.
{==+==}
此修饰符仅与 `static` 链接类型兼容。使用任何其他类型都会导致编译器错误。
{==+==}


{==+==}
When building a rlib or staticlib `+bundle` means that the native static library
will be packed into the rlib or staticlib archive, and then retrieved from there
during linking of the final binary.
{==+==}
在构建 rlib 或 staticlib 时， `+bundle` 表示本地静态库将被打包到 rlib 或 staticlib 归档中，然后在链接最终二进制文件时从其中检索出来。
{==+==}


{==+==}
When building a rlib `-bundle` means that the native static library is registered as a dependency
of that rlib "by name", and object files from it are included only during linking of the final
binary, the file search by that name is also performed during final linking. \
When building a staticlib `-bundle` means that the native static library is simply not included
into the archive and some higher level build system will need to add it later during linking of
the final binary.
{==+==}
在构建 rlib 时， `-bundle` 表示本地静态库以名称的方式注册为该 rlib 的依赖项，并且其中的目标文件仅在链接最终二进制文件时包含，文件搜索也在最终链接期间按该名称执行。\
在构建 staticlib 时， `-bundle` 表示本地静态库不会被包含在档案中，某些更高级别的构建系统需要在链接最终二进制文件时随后添加它。
{==+==}


{==+==}
This modifier has no effect when building other targets like executables or dynamic libraries.
{==+==}
当构建其他目标 (如可执行文件或动态库) 时，此修饰符不起作用。
{==+==}


{==+==}
The default for this modifier is `+bundle`.
{==+==}
修饰符的默认值是 `+bundle` 。
{==+==}


{==+==}
More implementation details about this modifier can be found in
[`bundle` documentation for rustc].
{==+==}
关于这个修饰符的更多实现细节可在 [rustc 文档 `bundle`][`bundle` documentation for rustc] 找到 。
{==+==}


{==+==}
#### Linking modifiers: `whole-archive`
{==+==}
#### 链接修饰符: `whole-archive`
{==+==}


{==+==}
`+whole-archive` means that the static library is linked as a whole archive
without throwing any object files away.
{==+==}
`+whole-archive` 表示静态库作为整个归档进行链接，而不会丢弃任何对象文件。
{==+==}


{==+==}
The default for this modifier is `-whole-archive`.
{==+==}
这个修饰符的默认值是 `-whole-archive` 。
{==+==}


{==+==}
More implementation details about this modifier can be found in
[`whole-archive` documentation for rustc].
{==+==}
关于这个修改器的更多实现细节可在 [rustc 文档 `whole-archive`][`whole-archive` documentation for rustc] 中找到。
{==+==}


{==+==}
### Linking modifiers: `verbatim`
{==+==}
### 链接修饰符: `verbatim`
{==+==}


{==+==}
This modifier is compatible with all linking kinds.
{==+==}
此修饰符与所有链接类型兼容。
{==+==}


{==+==}
`+verbatim` means that rustc itself won't add any target-specified library prefixes or suffixes
(like `lib` or `.a`) to the library name, and will try its best to ask for the same thing from the
linker.
{==+==}
`+verbatim` 表示 rustc 本身不会为库名称添加任何目标指定的库前缀或后缀 (如 `lib` 或 `.a` ) ，并尝试尽可能要求链接器也是如此。
{==+==}


{==+==}
`-verbatim` means that rustc will either add a target-specific prefix and suffix to the library
name before passing it to linker, or won't prevent linker from implicitly adding it.
{==+==}
`-verbatim` 表示 rustc 将在将库名称传递给链接器之前添加特定目标的前缀和后缀，且不会阻止链接器隐式添加它。
{==+==}


{==+==}
The default for this modifier is `-verbatim`.
{==+==}
这个修饰符的默认值是 `-verbatim` 。
{==+==}


{==+==}
More implementation details about this modifier can be found in
[`verbatim` documentation for rustc].
{==+==}
关于这个修饰符的更多实现细节可在 [rustc 文档 `verbatim`][`verbatim` documentation for rustc] 中找到。
{==+==}


{==+==}
#### `dylib` versus `raw-dylib`
{==+==}
#### `dylib` 对比 `raw-dylib`
{==+==}


{==+==}
On Windows, linking against a dynamic library requires that an import library
is provided to the linker: this is a special static library that declares all
of the symbols exported by the dynamic library in such a way that the linker
knows that they have to be dynamically loaded at runtime.
{==+==}
在 Windows 上，链接动态库需要向链接器提供导入库：这是一个特殊的静态库，以这样一种方式声明动态库导出的所有符号，使得链接器知道它们必须在运行时动态加载。
{==+==}


{==+==}
Specifying `kind = "dylib"` instructs the Rust compiler to link an import
library based on the `name` key. The linker will then use its normal library
resolution logic to find that import library. Alternatively, specifying
`kind = "raw-dylib"` instructs the compiler to generate an import library
during compilation and provide that to the linker instead.
{==+==}
指定 `kind = "dylib"` 会指示 Rust 编译器基于 `name` 键链接导入库。
然后，链接器将使用其正常的库解析逻辑来找到该导入库。另外，指定 `kind = "raw-dylib"` 会指示编译器在编译期间生成一个导入库，并提供给链接器。
{==+==}


{==+==}
`raw-dylib` is only supported on Windows and not supported on 32-bit x86
(`target_arch="x86"`). Using it when targeting other platforms or
x86 on Windows will result in a compiler error.
{==+==}
只有在 Windows 上才支持 `raw-dylib` ，不支持 32 位 x86 ( `target_arch="x86"` ) 。将其用于针对其他平台或 Windows 上的 x86 时将导致编译器错误。
{==+==}


{==+==}
### The `link_name` attribute
{==+==}
### `link_name` 属性
{==+==}


{==+==}
The *`link_name` attribute* may be specified on declarations inside an `extern`
block to indicate the symbol to import for the given function or static. It
uses the [_MetaNameValueStr_] syntax to specify the name of the symbol.
{==+==}
在 `extern` 块内的声明上可以指定 *`link_name` 属性* ，以指示要为给定函数或静态导入的符号。
使用 [_元名称值字符串_][_MetaNameValueStr_] 语法来指定符号的名称。
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
将此属性与 `link_ordinal` 属性一起使用会导致编译器错误。
{==+==}


{==+==}
### The `link_ordinal` attribute
{==+==}
### `link_ordinal` 属性
{==+==}


{==+==}
The *`link_ordinal` attribute* can be applied on declarations inside an `extern`
block to indicate the numeric ordinal to use when generating the import library
to link against. An ordinal is a unique number per symbol exported by a dynamic
library on Windows and can be used when the library is being loaded to find
that symbol rather than having to look it up by name.
{==+==}
可以在 `extern` 块内的声明上应用 *`link_ordinal` 属性*，以指示生成要链接的导入库时要使用的数字序数。 
在 Windows 上，每个动态库导出的符号都有一个唯一的编号，可以在加载库时使用该编号查找该符号，而不必通过名称查找它。
{==+==}


{==+==}
Warning: `link_ordinal` should only be used in cases where the ordinal of the
symbol is known to be stable: if the ordinal of a symbol is not explicitly set
when its containing binary is built then one will be automatically assigned to
it, and that assigned ordinal may change between builds of the binary.
{==+==}
警告：`link_ordinal` 只应在符号的序数已知为稳定时使用：如果没有在构建其所在二进制文件时显式设置符号的序数，则将自动分配一个序数，而该分配的序数可能会在二进制文件的不同构建之间发生变化。
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
此属性仅与 `raw-dylib` 链接类型一起使用。使用任何其他类型都会导致编译器错误。
{==+==}


{==+==}
Using this attribute with the `link_name` attribute will result in a
compiler error.
{==+==}
将此属性与 `link_name` 属性一起使用将导致编译器错误。
{==+==}


{==+==}
### Attributes on function parameters
{==+==}
### 函数参数的属性
{==+==}


{==+==}
Attributes on extern function parameters follow the same rules and
restrictions as [regular function parameters].
{==+==}
外部函数参数的属性遵循与 [常规函数参数][regular function parameters] 相同的规则和限制。
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