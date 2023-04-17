{==+==}
# Modules
{==+==}
# 模块
{==+==}


{==+==}
> **<sup>Syntax:</sup>**\
> _Module_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `unsafe`<sup>?</sup> `mod` [IDENTIFIER] `;`\
> &nbsp;&nbsp; | `unsafe`<sup>?</sup> `mod` [IDENTIFIER] `{`\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_Item_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; `}`
{==+==}
> **<sup>语法:</sup>**\
> _模块_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `unsafe`<sup>?</sup> `mod` [标识符][IDENTIFIER] `;`\
> &nbsp;&nbsp; | `unsafe`<sup>?</sup> `mod` [标识符][IDENTIFIER] `{`\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_条目_][_Item_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; `}`
{==+==}


{==+==}
A module is a container for zero or more [items].
{==+==}
模块是包含零个或多个 [条目][items] 的容器。
{==+==}


{==+==}
A _module item_ is a module, surrounded in braces, named, and prefixed with the
keyword `mod`. A module item introduces a new, named module into the tree of
modules making up a crate. Modules can nest arbitrarily.
{==+==}
一个 _模块条目_ 是一个用花括号括起来的、以 `mod` 关键字为前缀的、命名的模块。
模块条目将一个新的命名模块引入到组成 crate 的模块树中。模块可以任意嵌套。
{==+==}


{==+==}
An example of a module:
{==+==}
一个模块的例子:
{==+==}


{==+==}
```rust
mod math {
    type Complex = (f64, f64);
    fn sin(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn cos(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn tan(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
}
```
{==+==}

{==+==}


{==+==}
Modules and types share the same namespace. Declaring a named type with the
same name as a module in scope is forbidden: that is, a type definition, trait,
struct, enumeration, union, type parameter or crate can't shadow the name of a
module in scope, or vice versa. Items brought into scope with `use` also have
this restriction.
{==+==}
模块和类型共享相同的命名空间。
在作用域中使用与模块同名的类型是被禁止的：也就是说，类型定义、trait、struct、enum、union、类型参数或 crate 不能隐藏作用域中模块的名称，反之亦然。使用 `use` 引入作用域的条目也有此限制。
{==+==}


{==+==}
The `unsafe` keyword is syntactically allowed to appear before the `mod`
keyword, but it is rejected at a semantic level. This allows macros to consume
the syntax and make use of the `unsafe` keyword, before removing it from the
token stream.
{==+==}
`unsafe` 关键字在 `mod` 关键字之前语法上是允许的，但在语义层面上被拒绝。
这允许宏消耗语法并使用 `unsafe` 关键字，然后从 token 流中删除它。
{==+==}


{==+==}
## Module Source Filenames
{==+==}
## 模块源码文件名
{==+==}


{==+==}
A module without a body is loaded from an external file. When the module does
not have a `path` attribute, the path to the file mirrors the logical [module
path]. Ancestor module path components are directories, and the module's
contents are in a file with the name of the module plus the `.rs` extension.
For example, the following module structure can have this corresponding
filesystem structure:
{==+==}
一个没有主体的模块将从外部文件加载。
当模块没有 `path` 属性时，文件的路径与逻辑 [模块路径][module path] 相同。
祖先模块路径组件是目录，而模块的内容在一个名为该模块加上 `.rs` 扩展名的文件中。
例如，下面的模块结构可以有如下的文件系统结构：
{==+==}


{==+==}
Module Path               | Filesystem Path  | File Contents
------------------------- | ---------------  | -------------
`crate`                   | `lib.rs`         | `mod util;`
`crate::util`             | `util.rs`        | `mod config;`
`crate::util::config`     | `util/config.rs` |
{==+==}
模块路径                  | 文件系统路径      | 文件内容
------------------------- | ---------------  | -------------
`crate`                   | `lib.rs`         | `mod util;`
`crate::util`             | `util.rs`        | `mod config;`
`crate::util::config`     | `util/config.rs` |
{==+==}


{==+==}
Module filenames may also be the name of the module as a directory with the
contents in a file named `mod.rs` within that directory. The above example can
alternately be expressed with `crate::util`'s contents in a file named
`util/mod.rs`. It is not allowed to have both `util.rs` and `util/mod.rs`.
{==+==}
模块文件名也可以是模块的名称作为目录，该目录中的内容在名为 `mod.rs` 的文件中。
上面的例子也可以使用名称为 `util/mod.rs` 的文件来表达 `crate::util` 的内容。
不允许同时存在 `util.rs` 和 `util/mod.rs`。
{==+==}


{==+==}
> **Note**: Prior to `rustc` 1.30, using `mod.rs` files was the way to load
> a module with nested children. It is encouraged to use the new naming
> convention as it is more consistent, and avoids having many files named
> `mod.rs` within a project.
{==+==}
> **注意**: 注意：在 Rust 1.30 之前，使用 `mod.rs` 文件是一种加载具有嵌套子项模块的方法。推荐使用新的命名约定，因为它更一致，并避免在项目中有许多名为 `mod.rs` 的文件。
{==+==}


{==+==}
### The `path` attribute
{==+==}
### `path` 属性
{==+==}


{==+==}
The directories and files used for loading external file modules can be
influenced with the `path` attribute.
{==+==}
`path` 属性可以影响加载外部文件模块时使用的目录和文件。
{==+==}


{==+==}
For `path` attributes on modules not inside inline module blocks, the file
path is relative to the directory the source file is located. For example, the
following code snippet would use the paths shown based on where it is located:
{==+==}
对于不在内联模块块中的模块上的 `path` 属性，文件路径相对于源文件所在的目录。
例如，以下代码片段会使用根据其位置而显示的路径:
{==+==}


{==+==}
<!-- ignore: requires external files -->
```rust,ignore
#[path = "foo.rs"]
mod c;
```
{==+==}

{==+==}


{==+==}
Source File    | `c`'s File Location | `c`'s Module Path
-------------- | ------------------- | ----------------------
`src/a/b.rs`   | `src/a/foo.rs`      | `crate::a::b::c`
`src/a/mod.rs` | `src/a/foo.rs`      | `crate::a::c`
{==+==}
源代码文件     | `c` 的文件位置      | `c` 的模块路径
-------------- | ------------------- | ----------------------
`src/a/b.rs`   | `src/a/foo.rs`      | `crate::a::b::c`
`src/a/mod.rs` | `src/a/foo.rs`      | `crate::a::c`
{==+==}


{==+==}
For `path` attributes inside inline module blocks, the relative location of
the file path depends on the kind of source file the `path` attribute is
located in. "mod-rs" source files are root modules (such as `lib.rs` or
`main.rs`) and modules with files named `mod.rs`. "non-mod-rs" source files
are all other module files. Paths for `path` attributes inside inline module
blocks in a mod-rs file are relative to the directory of the mod-rs file
including the inline module components as directories. For non-mod-rs files,
it is the same except the path starts with a directory with the name of the
non-mod-rs module. For example, the following code snippet would use the paths
shown based on where it is located:
{==+==}
对于嵌套在内联模块块中的 `path` 属性，文件路径的相对位置取决于 `path` 属性所在的源文件类型。
"mod-rs" 源文件是根模块 (例如 `lib.rs` 或 `main.rs` ) 以及文件名为 `mod.rs` 的模块。
"non-mod-rs" 源文件是所有其他模块文件。
在 mod-rs 文件中的内联模块块的 `path` 属性的路径是相对于 mod-rs 文件的目录，包括内联模块作为目录的组件。
对于非 mod-rs 文件，它是相同的，只是路径以非 mod-rs 模块的名称开头的目录。
例如，以下代码片段将根据其位置使用所示路径:
{==+==}


{==+==}
<!-- ignore: requires external files -->
```rust,ignore
mod inline {
    #[path = "other.rs"]
    mod inner;
}
```
{==+==}

{==+==}


{==+==}
Source File    | `inner`'s File Location   | `inner`'s Module Path
-------------- | --------------------------| ----------------------------
`src/a/b.rs`   | `src/a/b/inline/other.rs` | `crate::a::b::inline::inner`
`src/a/mod.rs` | `src/a/inline/other.rs`   | `crate::a::inline::inner`
{==+==}
源代码文件     | `inner` 的文件位置        | `inner` 的模块路径
-------------- | --------------------------| ----------------------------
`src/a/b.rs`   | `src/a/b/inline/other.rs` | `crate::a::b::inline::inner`
`src/a/mod.rs` | `src/a/inline/other.rs`   | `crate::a::inline::inner`
{==+==}


{==+==}
An example of combining the above rules of `path` attributes on inline modules
and nested modules within (applies to both mod-rs and non-mod-rs files):
{==+==}
一个结合了内联模块以及其中嵌套模块的 `path` 属性的规则的示例 (适用于 mod-rs 和非 mod-rs 文件):
{==+==}


{==+==}
<!-- ignore: requires external files -->
```rust,ignore
#[path = "thread_files"]
mod thread {
    // Load the `local_data` module from `thread_files/tls.rs` relative to
    // this source file's directory.
    #[path = "tls.rs"]
    mod local_data;
}
```
{==+==}
<!-- ignore: requires external files -->
```rust,ignore
#[path = "thread_files"]
mod thread {
    // 从 `thread_files/tls.rs` 中加载 `local_data` 模块，相对于这个源文件的目录。
    #[path = "tls.rs"]
    mod local_data;
}
```
{==+==}


{==+==}
## Attributes on Modules
{==+==}
## 模块的属性
{==+==}


{==+==}
Modules, like all items, accept outer attributes. They also accept inner
attributes: either after `{` for a module with a body, or at the beginning of the
source file, after the optional BOM and shebang.
{==+==}
模块和所有条目一样，可以接受外部属性 (outer attributes) ，同时也可以接受内部属性 (inner attributes) ，
内部属性可以出现在模块主体的左括号 `{` 之后，也可以出现在源文件的开头，在可选的 BOM 和 shebang 之后。
{==+==}


{==+==}
The built-in attributes that have meaning on a module are [`cfg`],
[`deprecated`], [`doc`], [the lint check attributes], [`path`], and
[`no_implicit_prelude`]. Modules also accept macro attributes.
{==+==}
内置的对模块有意义的属性包括 [`cfg`] 、 [`deprecated`] 、 [`doc`] 、 [lint 检查属性][the lint check attributes] 、 [`path`] 和 [`no_implicit_prelude`] 。模块还接受宏属性。
{==+==}


{==+==}
[_InnerAttribute_]: ../attributes.md
[_Item_]: ../items.md
[`cfg`]: ../conditional-compilation.md
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[`no_implicit_prelude`]: ../names/preludes.md#the-no_implicit_prelude-attribute
[`path`]: #the-path-attribute
[IDENTIFIER]: ../identifiers.md
[attribute]: ../attributes.md
[items]: ../items.md
[module path]: ../paths.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
{==+==}

{==+==}


{==+==}
<script>
(function() {
    var fragments = {
        "#prelude-items": "../names/preludes.html",
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