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

{==+==}


{==+==}
A module is a container for zero or more [items].
{==+==}
module "模块" 是容纳零个或多个 [items] "条目" 的容器。
{==+==}


{==+==}
A _module item_ is a module, surrounded in braces, named, and prefixed with the
keyword `mod`. A module item introduces a new, named module into the tree of
modules making up a crate. Modules can nest arbitrarily.
{==+==}
一个 _模块条目_ 是一个模块，用大括号包围，命名，并以关键字 `mod` 为前缀。
一个模块条目将一个新的、命名的模块引入构成crate的模块树中。模块可以任意嵌套。
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
模块和类型共享同一个命名空间。
禁止声明与作用域中的模块同名的类型：也就是说，一个类型定义、trait、struct、enumeration、union、类型参数或crate无法隐藏作用域中的模块名称，反之亦然。用 `use` 引入作用域的条目也有这个限制。
{==+==}


{==+==}
The `unsafe` keyword is syntactically allowed to appear before the `mod`
keyword, but it is rejected at a semantic level. This allows macros to consume
the syntax and make use of the `unsafe` keyword, before removing it from the
token stream.
{==+==}
语法上允许 `unsafe` 关键字出现在 `mod` 关键字之前，但在语义层面上被拒绝。
这允许宏消耗句法(syntax)并使用 `unsafe` 关键字，然后再从token流中删除它。
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
从外部文件加载一个没有主体的模块。
当模块没有 `path` 属性时，文件的路径映射为逻辑上的 [module path]。
父级的模块路径部分是目录，模块内容在一个文件中，文件名是模块名称加上 `.rs` 扩展名。
比如，下面的模块结构可以有这种相应的文件系统结构:
{==+==}


{==+==}
Module Path               | Filesystem Path  | File Contents
------------------------- | ---------------  | -------------
`crate`                   | `lib.rs`         | `mod util;`
`crate::util`             | `util.rs`        | `mod config;`
`crate::util::config`     | `util/config.rs` |
{==+==}

{==+==}


{==+==}
Module filenames may also be the name of the module as a directory with the
contents in a file named `mod.rs` within that directory. The above example can
alternately be expressed with `crate::util`'s contents in a file named
`util/mod.rs`. It is not allowed to have both `util.rs` and `util/mod.rs`.
{==+==}
模块文件名也可以作为一个目录的模块名称，其内容在该目录中名为 `mod.rs` 的文件中。
上面的例子可以把 `crate::util` 的内容放在一个名为 `util/mod.rs` 的文件中。
不允许同时拥有 `util.rs` 和 `util/mod.rs` 。
{==+==}


{==+==}
> **Note**: Prior to `rustc` 1.30, using `mod.rs` files was the way to load
> a module with nested children. It is encouraged to use the new naming
> convention as it is more consistent, and avoids having many files named
> `mod.rs` within a project.
{==+==}
> **注意**: 在 `rustc` 1.30 之前，使用 `mod.rs` 文件是加载一个有嵌套子模块的方法。
> 鼓励使用新的命名规则，因为它更加一致，并避免在项目中出现许多名为 `mod.rs` 的文件。
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
可以用 `path` 属性来影响加载外部文件模块的目录和文件。
{==+==}


{==+==}
For `path` attributes on modules not inside inline module blocks, the file
path is relative to the directory the source file is located. For example, the
following code snippet would use the paths shown based on where it is located:
{==+==}
对于不在内联模块块内的，模块的 `path` 属性，文件路径是相对于源文件所在的目录。
比如，下面的代码片断将根据它的位置使用显示的路径。
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
对于内联模块块内的 `path` 属性，文件路径的相对位置取决于 `path` 属性所在的源文件的种类。
`mod.rs` 源文件是根模块 (如 `lib.rs` 或 `main.rs` ) 和文件名为 `mod.rs` 的模块。
"non-mod-rs" 源文件是所有其他模块文件。在 mod-rs 文件中内联模块块内的 `path` 属性的路径是相对于 mod-rs 文件的目录，包括作为目录的内联模块组件。
对于非 mod-rs 文件，除了路径以非mod-rs模块名称的目录开始，其他都是一样的。
比如，下面的代码片断将根据它的位置使用所示的路径:
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

{==+==}


{==+==}
An example of combining the above rules of `path` attributes on inline modules
and nested modules within (applies to both mod-rs and non-mod-rs files):
{==+==}
一个结合上述内联模块和嵌套模块的 `path` 属性规则的例子 (适用于mod-rs和非mod-rs文件):
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
模块，像所有条目一样，接受外部属性。
也接受内部属性: 要么在有主体的模块的 `{` 之后，要么在源文件的开头，在可选的BOM和shebang "执行注解" 之后。
{==+==}


{==+==}
The built-in attributes that have meaning on a module are [`cfg`],
[`deprecated`], [`doc`], [the lint check attributes], [`path`], and
[`no_implicit_prelude`]. Modules also accept macro attributes.
{==+==}
对模块有意义的内置属性是 [`cfg`] , [`deprecated`] , [`doc`] , [the lint check attributes] , [`path`] , 和 [`no_implicit_prelude`] 。模块也接受宏属性。
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