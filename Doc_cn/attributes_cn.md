{==+==}
{{#include attributes-redirect.html}}
# Attributes
{==+==}
{{#include attributes-redirect.html}}
# 属性
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _InnerAttribute_ :\
> &nbsp;&nbsp; `#` `!` `[` _Attr_ `]`
>
> _OuterAttribute_ :\
> &nbsp;&nbsp; `#` `[` _Attr_ `]`
>
> _Attr_ :\
> &nbsp;&nbsp; [_SimplePath_] _AttrInput_<sup>?</sup>
>
> _AttrInput_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_DelimTokenTree_]\
> &nbsp;&nbsp; | `=` [_Expression_]
{==+==}

{==+==}


{==+==}
An _attribute_ is a general, free-form metadatum that is interpreted according
to name, convention, language, and compiler version. Attributes are modeled
on Attributes in [ECMA-335], with the syntax coming from [ECMA-334] \(C#).
{==+==}
 _attribute_ "属性" 是通用的、自由形式的元数据，根据名称、约定、语言和编译器版本来解释。
属性以 [ECMA-335] 中的 Attributes 为模型，其语法来自 [ECMA-334] \(C#) 。
{==+==}


{==+==}
_Inner attributes_, written with a bang (`!`) after the hash (`#`), apply to the
item that the attribute is declared within. _Outer attributes_, written without
the bang after the hash, apply to the thing that follows the attribute.
{==+==}
 _内部属性_ ，在井号 (`#`) 后跟一个叹号 (`!`)，应用于内部声明属性的条目。
 _外部属性_ ，在哈希值后不加叹号，应用于该属性随后的事物。
{==+==}


{==+==}
The attribute consists of a path to the attribute, followed by an optional
delimited token tree whose interpretation is defined by the attribute.
Attributes other than macro attributes also allow the input to be an equals
sign (`=`) followed by an expression. See the [meta item
syntax](#meta-item-attribute-syntax) below for more details.
{==+==}
属性由一个指向该属性的路径组成，后面是一个可选择的分隔 token 树，其解释由该属性定义。
除宏属性外，其他属性也允许输入为等号 (`=`) ，后面跟一个表达式。更多细节见下面的 [meta item syntax](#meta-item-attribute-syntax) "元条目语法" 。
{==+==}


{==+==}
Attributes can be classified into the following kinds:
{==+==}
属性可分为以下几种。
{==+==}


{==+==}
* [Built-in attributes]
* [Macro attributes][attribute macros]
* [Derive macro helper attributes]
* [Tool attributes](#tool-attributes)
{==+==}
* [内置属性][Built-in attributes]
* [宏属性][attribute macros]
* [派生宏辅助属性][Derive macro helper attributes]
* [工具属性](#tool-attributes)
{==+==}


{==+==}
Attributes may be applied to many things in the language:
{==+==}
属性可以应用于语言中的许多事物:
{==+==}


{==+==}
* All [item declarations] accept outer attributes while [external blocks],
  [functions], [implementations], and [modules] accept inner attributes.
{==+==}
* 所有的 [条目声明][item declarations] 都接受外部属性，而 [外部块][external blocks] 、 [函数][functions] 、 [实现][implementations] 和 [模块][modules] 接受内部属性。
{==+==}


{==+==}
* Most [statements] accept outer attributes (see [Expression Attributes] for
  limitations on expression statements).
{==+==}
* 大多数 [语句][statements] 都接受外部属性 (关于表达式语句的限制，见 [表达式属性][Expression Attributes] ) 。
{==+==}


{==+==}
* [Block expressions] accept outer and inner attributes, but only when they are
  the outer expression of an [expression statement] or the final expression of
  another block expression.
{==+==}
* [块表达式][Block expressions] 接受外部和内部属性，但只有当它们是 [表达式语句][expression statement] 的外部表达式或另一个块表达式的最终表达式时才接受。
{==+==}


{==+==}
* [Enum] variants and [struct] and [union] fields accept outer attributes.
* [Match expression arms][match expressions] accept outer attributes.
* [Generic lifetime or type parameter][generics] accept outer attributes.
{==+==}
* [Enum] 变体和 [struct] 和 [union] 字段接受外部属性。
* [匹配表达式分支][match expressions] 接受外部属性。
* [泛型生命周期或类型参数][generics] 接受外部属性。
{==+==}


{==+==}
* Expressions accept outer attributes in limited situations, see [Expression
  Attributes] for details.
{==+==}
* 表达式在有限的情况下接受外部属性，详见 [表达式属性][Expression Attributes] 。
{==+==}


{==+==}
* [Function][functions], [closure] and [function pointer]
  parameters accept outer attributes. This includes attributes on variadic parameters
  denoted with `...` in function pointers and [external blocks][variadic functions].
{==+==}
* [功能][functions] 、 [闭包][closure] 和 [函数指针][function pointer] 参数接受外部属性。这包括在函数指针和 [外部块][variadic functions] 中用 `...` 表示的变量参数的属性。
{==+==}


{==+==}
Some examples of attributes:
{==+==}
一些属性的例子:
{==+==}


{==+==}
```rust
// General metadata applied to the enclosing module or crate.
#![crate_type = "lib"]

// A function marked as a unit test
#[test]
fn test_foo() {
    /* ... */
}

// A conditionally-compiled module
#[cfg(target_os = "linux")]
mod bar {
    /* ... */
}

// A lint attribute used to suppress a warning/error
#[allow(non_camel_case_types)]
type int8_t = i8;

// Inner attribute applies to the entire function.
fn some_unused_variables() {
  #![allow(unused_variables)]

  let x = ();
  let y = ();
  let z = ();
}
```
{==+==}
```rust
// 应用于封闭模块或 crate 的通用 metadata 。
#![crate_type = "lib"]

// 标记为单元测试的函数
#[test]
fn test_foo() {
    /* ... */
}

// 条件编译模块
#[cfg(target_os = "linux")]
mod bar {
    /* ... */
}

// 用于屏蔽警告/错误的 lint 属性
#[allow(non_camel_case_types)]
type int8_t = i8;

// 将内部属性应用于整个函数。
fn some_unused_variables() {
  #![allow(unused_variables)]

  let x = ();
  let y = ();
  let z = ();
}
```
{==+==}


{==+==}
## Meta Item Attribute Syntax
{==+==}
## 元条目属性语法
{==+==}


{==+==}
A "meta item" is the syntax used for the _Attr_ rule by most [built-in
attributes]. It has the following grammar:
{==+==}
 "元条目" 是大多数 [内置属性][built-in attributes] 对 _Attr_ 规则所使用的语法。它有以下的语法:
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MetaItem_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_SimplePath_]\
> &nbsp;&nbsp; | [_SimplePath_] `=` [_Expression_]\
> &nbsp;&nbsp; | [_SimplePath_] `(` _MetaSeq_<sup>?</sup> `)`
>
> _MetaSeq_ :\
> &nbsp;&nbsp; _MetaItemInner_ ( `,` MetaItemInner )<sup>\*</sup> `,`<sup>?</sup>
>
> _MetaItemInner_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _MetaItem_\
> &nbsp;&nbsp; | [_Expression_]
{==+==}

{==+==}


{==+==}
Expressions in meta items must macro-expand to literal expressions, which must not
include integer or float type suffixes. Expressions which are not literal expressions
will be syntactically accepted (and can be passed to proc-macros), but will be rejected after parsing.
{==+==}
元条目中的表达式必须宏展开为字面值表达式，其中不能包含整数或浮点数类型的后缀。
在语法上可以接受不是字面值表达式的表达式 (并且可以传递给过程宏)，但是在语法解析后会被拒绝。
{==+==}


{==+==}
Note that if the attribute appears within another macro, it will be expanded
after that outer macro. For example, the following code will expand the
`Serialize` proc-macro first, which must preserve the `include_str!` call in
order for it to be expanded:
{==+==}
注意，如果该属性出现在另一个宏中，它将在该外部宏之后被展开。
例如，下面的代码将首先展开 `Serialize` 过程宏，必须保留 `include_str!` 的调用，才能被展开:
{==+==}


{==+==}
```rust ignore
#[derive(Serialize)]
struct Foo {
    #[doc = include_str!("x.md")]
    x: u32
}
```
{==+==}

{==+==}


{==+==}
Additionally, macros in attributes will be expanded only after all other attributes applied to the item:
{==+==}
此外，属性中的宏将只在应用于条目的所有其他属性之后展开。
{==+==}


{==+==}
```rust ignore
#[macro_attr1] // expanded first
#[doc = mac!()] // `mac!` is expanded fourth.
#[macro_attr2] // expanded second
#[derive(MacroDerive1, MacroDerive2)] // expanded third
fn foo() {}
```
{==+==}
```rust ignore
#[macro_attr1] // 首先展开
#[doc = mac!()] // `mac!` 第4展开
#[macro_attr2] // 第二展开
#[derive(MacroDerive1, MacroDerive2)] // 第三展开
fn foo() {}
```
{==+==}


{==+==}
Various built-in attributes use different subsets of the meta item syntax to
specify their inputs. The following grammar rules show some commonly used
forms:
{==+==}
各种内置属性使用元条目语法的不同子集来指定它们的输入。下面的语法规则显示了一些常用的形式:
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MetaWord_:\
> &nbsp;&nbsp; [IDENTIFIER]
>
> _MetaNameValueStr_:\
> &nbsp;&nbsp; [IDENTIFIER] `=` ([STRING_LITERAL] | [RAW_STRING_LITERAL])
>
> _MetaListPaths_:\
> &nbsp;&nbsp; [IDENTIFIER] `(` ( [_SimplePath_] (`,` [_SimplePath_])* `,`<sup>?</sup> )<sup>?</sup> `)`
>
> _MetaListIdents_:\
> &nbsp;&nbsp; [IDENTIFIER] `(` ( [IDENTIFIER] (`,` [IDENTIFIER])* `,`<sup>?</sup> )<sup>?</sup> `)`
>
> _MetaListNameValueStr_:\
> &nbsp;&nbsp; [IDENTIFIER] `(` ( _MetaNameValueStr_ (`,` _MetaNameValueStr_)* `,`<sup>?</sup> )<sup>?</sup> `)`
{==+==}

{==+==}


{==+==}
Some examples of meta items are:
{==+==}
元条目的一些例子:
{==+==}


{==+==}
Style | Example
------|--------
_MetaWord_ | `no_std`
_MetaNameValueStr_ | `doc = "example"`
_MetaListPaths_ | `allow(unused, clippy::inline_always)`
_MetaListIdents_ | `macro_use(foo, bar)`
_MetaListNameValueStr_ | `link(name = "CoreFoundation", kind = "framework")`
{==+==}

{==+==}


{==+==}
## Active and inert attributes
{==+==}
## 活动和惰性属性
{==+==}


{==+==}
An attribute is either active or inert. During attribute processing, *active
attributes* remove themselves from the thing they are on while *inert attributes*
stay on.
{==+==}
一个属性要么是活动的，要么是惰性的。在属性处理过程中，*活动属性* 从它们所在的事物上移走， 而 *惰性属性* 保持不变。
{==+==}


{==+==}
The [`cfg`] and [`cfg_attr`] attributes are active. The [`test`] attribute is
inert when compiling for tests and active otherwise. [Attribute macros] are
active. All other attributes are inert.
{==+==}
[`cfg`] 和 [`cfg_attr`] 属性是活动的。当测试编译时 [`test`] 属性是惰性的，否则是活动的。
[Attribute macros] 是活动的。所有其他属性都是惰性的。
{==+==}


{==+==}
## Tool attributes
{==+==}
## 工具属性
{==+==}


{==+==}
The compiler may allow attributes for external tools where each tool resides
in its own namespace in the [tool prelude]. The first segment of the attribute
path is the name of the tool, with one or more additional segments whose
interpretation is up to the tool.
{==+==}
编译器可以允许外部工具属性，其中每个工具都驻留在 [工具预导入][tool prelude] 中自己的命名空间。
属性路径的第一段是工具的名称，还有一个或多个附加段，其解释由工具决定。
{==+==}


{==+==}
When a tool is not in use, the tool's attributes are accepted without a
warning. When the tool is in use, the tool is responsible for processing and
interpretation of its attributes.
{==+==}
当一个工具不使用时，允许工具属性，没有警告。
当工具在使用时，工具负责处理和解释其属性。
{==+==}


{==+==}
Tool attributes are not available if the [`no_implicit_prelude`] attribute is
used.
{==+==}
如果使用了 [no_implicit_prelude`] 属性，则工具属性不可用。
{==+==}


{==+==}
```rust
// Tells the rustfmt tool to not format the following element.
#[rustfmt::skip]
struct S {
}

// Controls the "cyclomatic complexity" threshold for the clippy tool.
#[clippy::cyclomatic_complexity = "100"]
pub fn f() {}
```
{==+==}
```rust
// 告知 rustfmt 工具不要格式化以下元素。
#[rustfmt::skip]
struct S {
}

// 控制 Clippy 工具的 "cyclomatic complexity" 阈值。
#[clippy::cyclomatic_complexity = "100"]
pub fn f() {}
```
{==+==}


{==+==}
> Note: `rustc` currently recognizes the tools "clippy" and "rustfmt".
{==+==}
> 注意: `rustc` 目前可以识别工具 "clippy" 和 "rustfmt" 。
{==+==}


{==+==}
## Built-in attributes index
{==+==}
## 内置属性索引
{==+==}


{==+==}
The following is an index of all built-in attributes.
{==+==}
以下是所有内置属性的索引。
{==+==}


{==+==}
- Conditional compilation
  - [`cfg`] — Controls conditional compilation.
  - [`cfg_attr`] — Conditionally includes attributes.
{==+==}
- 条件编译
  - [`cfg`] — 控制条件编译
  - [`cfg_attr`] — 条件包含属性
{==+==}


{==+==}
- Testing
  - [`test`] — Marks a function as a test.
  - [`ignore`] — Disables a test function.
  - [`should_panic`] — Indicates a test should generate a panic.
{==+==}
- 测试
  - [`test`] — 标记为测试函数
  - [`ignore`] — 禁用测试函数
  - [`should_panic`] — 表示测试应该产生恐慌。
{==+==}


{==+==}
- Derive
  - [`derive`] — Automatic trait implementations.
  - [`automatically_derived`] — Marker for implementations created by
    `derive`.
{==+==}
- 派生
  - [`derive`] — 自动 trait 实现
  - [`automatically_derived`] — 由 `derive` 创建的实现的标记
{==+==}


{==+==}
- Macros
  - [`macro_export`] — Exports a `macro_rules` macro for cross-crate usage.
  - [`macro_use`] — Expands macro visibility, or imports macros from other
    crates.
  - [`proc_macro`] — Defines a function-like macro.
  - [`proc_macro_derive`] — Defines a derive macro.
  - [`proc_macro_attribute`] — Defines an attribute macro.
{==+==}
- 宏
  - [`macro_export`] — 导出一个 `macro_rules` 宏，用于跨 crate 使用。
  - [`macro_use`] — 展开宏的可见性，或从其他 crate 导入宏。
  - [`proc_macro`] — 定义类函数宏。
  - [`proc_macro_derive`] — 定义派生宏。
  - [`proc_macro_attribute`] — 定义属性宏。
{==+==}


{==+==}
- Diagnostics
  - [`allow`], [`warn`], [`deny`], [`forbid`] — Alters the default lint level.
  - [`deprecated`] — Generates deprecation notices.
  - [`must_use`] — Generates a lint for unused values.
{==+==}
- 诊断
  - [`allow`], [`warn`], [`deny`], [`forbid`] — 改变默认的 lint 级别。
  - [`deprecated`] — 生成弃用通知
  - [`must_use`] — 生成未使用值的 lint 。
{==+==}


{==+==}
- ABI, linking, symbols, and FFI
  - [`link`] — Specifies a native library to link with an `extern` block.
  - [`link_name`] — Specifies the name of the symbol for functions or statics
    in an `extern` block.
  - [`link_ordinal`] — Specifies the ordinal of the symbol for functions or
    statics in an `extern` block.
  - [`no_link`] — Prevents linking an extern crate.
  - [`repr`] — Controls type layout.
  - [`crate_type`] — Specifies the type of crate (library, executable, etc.).
  - [`no_main`] — Disables emitting the `main` symbol.
  - [`export_name`] — Specifies the exported symbol name for a function or
    static.
  - [`link_section`] — Specifies the section of an object file to use for a
    function or static.
  - [`no_mangle`] — Disables symbol name encoding.
  - [`used`] — Forces the compiler to keep a static item in the output
    object file.
  - [`crate_name`] — Specifies the crate name.
{==+==}
- ABI, linking, symbols, and FFI
  - [`link`] — 指定本地库，与 `extern` 块链接。
  - [`link_name`] — 指定 `extern` 块中的函数或静态的符号名称。
  - [`link_ordinal`] — 指定 `extern` 块中的函数或静态符号的顺序。
  - [`no_link`] — 防止链接外部 crate 。
  - [`repr`] — Controls type layout.
  - [`crate_type`] — 指定 crate 的类型 (库、可执行文件等) 。
  - [`no_main`] — 禁止发送 `main` 符号。
  - [`export_name`] — 指定函数或静态的导出符号名称。
  - [`link_section`] — 指定一个对象文件的节，以用于函数或静态。
  - [`no_mangle`] — 禁用符号名称编码。
  - [`used`] — 强制编译器在输出对象文件中保留静态条目。
  - [`crate_name`] — 指定 crate 名称。
{==+==}


{==+==}
- Code generation
  - [`inline`] — Hint to inline code.
  - [`cold`] — Hint that a function is unlikely to be called.
  - [`no_builtins`] — Disables use of certain built-in functions.
  - [`target_feature`] — Configure platform-specific code generation.
  - [`track_caller`] - Pass the parent call location to `std::panic::Location::caller()`.
  - [`instruction_set`] - Specify the instruction set used to generate a functions code
{==+==}
- 代码生成
  - [`inline`] — 提示内联代码。
  - [`cold`] — 提示函数不太可能被调用。
  - [`no_builtins`] — 禁止使用某些内置函数。
  - [`target_feature`] — 配置特定平台的代码生成。
  - [`track_caller`] - 将父级调用位置传递给 `std::panic::Location::caller()`。
  - [`instruction_set`] - 指定用于生成函数代码的指令集
{==+==}


{==+==}
- Documentation
  - `doc` — Specifies documentation. See [The Rustdoc Book] for more
    information. [Doc comments] are transformed into `doc` attributes.
{==+==}
- 文档
  - `doc` — 指定文档。更多信息见 [The Rustdoc Book] 。 [文档注释][Doc comments] 被转化为 `doc` 属性。
{==+==}


{==+==}
- Preludes
  - [`no_std`] — Removes std from the prelude.
  - [`no_implicit_prelude`] — Disables prelude lookups within a module.
{==+==}
- 预导入
  - [`no_std`] — 从预导入中删除 std 。
  - [`no_implicit_prelude`] — 禁用模块内的预导入查询。
{==+==}


{==+==}
- Modules
  - [`path`] — Specifies the filename for a module.
{==+==}
- 模块
  - [`path`] — 指定模块的文件名。
{==+==}


{==+==}
- Limits
  - [`recursion_limit`] — Sets the maximum recursion limit for certain
    compile-time operations.
  - [`type_length_limit`] — Sets the maximum size of a polymorphic type.
{==+==}
- 范围
  - [`recursion_limit`] — 设置某些编译时操作的最大递归限制。
  - [`type_length_limit`] — 设置多态类型的最大规模。
{==+==}


{==+==}
- Runtime
  - [`panic_handler`] — Sets the function to handle panics.
  - [`global_allocator`] — Sets the global memory allocator.
  - [`windows_subsystem`] — Specifies the windows subsystem to link with.
{==+==}
- 运行时
  - [`panic_handler`] — 设定处理恐慌的函数。
  - [`global_allocator`] — 设置全局内存分配器。
  - [`windows_subsystem`] — 指定要链接的 windows 子系统。
{==+==}


{==+==}
- Features
  - `feature` — Used to enable unstable or experimental compiler features. See
    [The Unstable Book] for features implemented in `rustc`.
{==+==}
- 特性
  - `feature` — 用于启用不稳定或实验性的编译器特性。参见 [The Unstable Book] 关于在 `rustc` 中实现的特性。
{==+==}


{==+==}
- Type System
  - [`non_exhaustive`] — Indicate that a type will have more fields/variants
    added in future.
{==+==}
- 类型系统
  - [`non_exhaustive`] — 表示类型在将来会有更多的字段/变体加入。
{==+==}


{==+==}
[Doc comments]: comments.md#doc-comments
[ECMA-334]: https://www.ecma-international.org/publications/standards/Ecma-334.htm
[ECMA-335]: https://www.ecma-international.org/publications/standards/Ecma-335.htm
[Expression Attributes]: expressions.md#expression-attributes
[IDENTIFIER]: identifiers.md
[RAW_STRING_LITERAL]: tokens.md#raw-string-literals
[STRING_LITERAL]: tokens.md#string-literals
[The Rustdoc Book]: ../rustdoc/the-doc-attribute.html
[The Unstable Book]: ../unstable-book/index.html
[_DelimTokenTree_]: macros.md
[_Expression_]: expressions.md
[_SimplePath_]: paths.md#simple-paths
[`allow`]: attributes/diagnostics.md#lint-check-attributes
[`automatically_derived`]: attributes/derive.md#the-automatically_derived-attribute
[`cfg_attr`]: conditional-compilation.md#the-cfg_attr-attribute
[`cfg`]: conditional-compilation.md#the-cfg-attribute
[`cold`]: attributes/codegen.md#the-cold-attribute
[`crate_name`]: crates-and-source-files.md#the-crate_name-attribute
[`crate_type`]: linkage.md
[`deny`]: attributes/diagnostics.md#lint-check-attributes
[`deprecated`]: attributes/diagnostics.md#the-deprecated-attribute
[`derive`]: attributes/derive.md
[`export_name`]: abi.md#the-export_name-attribute
[`forbid`]: attributes/diagnostics.md#lint-check-attributes
[`global_allocator`]: runtime.md#the-global_allocator-attribute
[`ignore`]: attributes/testing.md#the-ignore-attribute
[`inline`]: attributes/codegen.md#the-inline-attribute
[`instruction_set`]: attributes/codegen.md#the-instruction_set-attribute
[`link_name`]: items/external-blocks.md#the-link_name-attribute
[`link_ordinal`]: items/external-blocks.md#the-link_ordinal-attribute
[`link_section`]: abi.md#the-link_section-attribute
[`link`]: items/external-blocks.md#the-link-attribute
[`macro_export`]: macros-by-example.md#path-based-scope
[`macro_use`]: macros-by-example.md#the-macro_use-attribute
[`must_use`]: attributes/diagnostics.md#the-must_use-attribute
[`no_builtins`]: attributes/codegen.md#the-no_builtins-attribute
[`no_implicit_prelude`]: names/preludes.md#the-no_implicit_prelude-attribute
[`no_link`]: items/extern-crates.md#the-no_link-attribute
[`no_main`]: crates-and-source-files.md#the-no_main-attribute
[`no_mangle`]: abi.md#the-no_mangle-attribute
[`no_std`]: names/preludes.md#the-no_std-attribute
[`non_exhaustive`]: attributes/type_system.md#the-non_exhaustive-attribute
[`panic_handler`]: runtime.md#the-panic_handler-attribute
[`path`]: items/modules.md#the-path-attribute
[`proc_macro_attribute`]: procedural-macros.md#attribute-macros
[`proc_macro_derive`]: procedural-macros.md#derive-macros
[`proc_macro`]: procedural-macros.md#function-like-procedural-macros
[`recursion_limit`]: attributes/limits.md#the-recursion_limit-attribute
[`repr`]: type-layout.md#representations
[`should_panic`]: attributes/testing.md#the-should_panic-attribute
[`target_feature`]: attributes/codegen.md#the-target_feature-attribute
[`test`]: attributes/testing.md#the-test-attribute
[`track_caller`]: attributes/codegen.md#the-track_caller-attribute
[`type_length_limit`]: attributes/limits.md#the-type_length_limit-attribute
[`used`]: abi.md#the-used-attribute
[`warn`]: attributes/diagnostics.md#lint-check-attributes
[`windows_subsystem`]: runtime.md#the-windows_subsystem-attribute
[attribute macros]: procedural-macros.md#attribute-macros
[block expressions]: expressions/block-expr.md
[built-in attributes]: #built-in-attributes-index
[derive macro helper attributes]: procedural-macros.md#derive-macro-helper-attributes
[enum]: items/enumerations.md
[expression statement]: statements.md#expression-statements
[external blocks]: items/external-blocks.md
[functions]: items/functions.md
[generics]: items/generics.md
[implementations]: items/implementations.md
[item declarations]: items.md
[match expressions]: expressions/match-expr.md
[modules]: items/modules.md
[statements]: statements.md
[struct]: items/structs.md
[tool prelude]: names/preludes.md#tool-prelude
[union]: items/unions.md
[closure]: expressions/closure-expr.md
[function pointer]: types/function-pointer.md
[variadic functions]: items/external-blocks.html#variadic-functions
{==+==}

{==+==}