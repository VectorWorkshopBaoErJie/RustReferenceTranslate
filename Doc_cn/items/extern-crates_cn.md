{==+==}
# Extern crate declarations
{==+==}
# 外部crate声明
{==+==}


{==+==}
> **<sup>Syntax:<sup>**\
> _ExternCrate_ :\
> &nbsp;&nbsp; `extern` `crate` _CrateRef_ _AsClause_<sup>?</sup> `;`
>
> _CrateRef_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `self`
>
> _AsClause_ :\
> &nbsp;&nbsp; `as` ( [IDENTIFIER] | `_` )
{==+==}
> **<sup>语法:<sup>**\
> _外部Crate_ :\
> &nbsp;&nbsp; `extern` `crate` _Crate引用_ _As子句_<sup>?</sup> `;`
>
> _Crate引用_ :\
> &nbsp;&nbsp; [标识符][IDENTIFIER] | `self`
>
> _As子句_ :\
> &nbsp;&nbsp; `as` ( [标识符][IDENTIFIER] | `_` )
{==+==}


{==+==}
An _`extern crate` declaration_ specifies a dependency on an external crate.
The external crate is then bound into the declaring scope as the [identifier]
provided in the `extern crate` declaration. Additionally, if the `extern
crate` appears in the crate root, then the crate name is also added to the
[extern prelude], making it automatically in scope in all modules. The `as`
clause can be used to bind the imported crate to a different name.
{==+==}
一个 _`extern crate` 声明_ 指定了一个对外部 crate 的依赖。
外部 crate 然后被绑定到 `extern crate` 声明中提供的标识符中。
此外，如果 `extern crate` 出现在 crate 根中，则 crate 名称也会被添加到 [extern 预定义][extern prelude] 中，在所有模块中自动处于作用域内。
可以使用 `as` 子句将导入的 crate 绑定到不同的名称。
{==+==}


{==+==}
The external crate is resolved to a specific `soname` at compile time, and a
runtime linkage requirement to that `soname` is passed to the linker for
loading at runtime. The `soname` is resolved at compile time by scanning the
compiler's library path and matching the optional `crate_name` provided against
the [`crate_name` attributes] that were declared on the external crate when it was
compiled. If no `crate_name` is provided, a default `name` attribute is assumed,
equal to the [identifier] given in the `extern crate` declaration.
{==+==}
编译时，外部 crate 会被解析为一个特定的 `soname`，并且对于该 `soname` 的运行时链接需求会在编译时传递给链接器进行加载。
编译时，编译器会扫描其库路径，并根据外部 crate 编译时声明的 [`crate_name` 属性][`crate_name` attributes] 与可选的 `crate_name` 进行匹配，以解析出 `soname`。
如果没有提供 `crate_name`，则会假定一个默认的 `name` 属性，等于 `extern crate` 声明中给出的 [标识符][identifier]。
{==+==}


{==+==}
The `self` crate may be imported which creates a binding to the current crate.
In this case the `as` clause must be used to specify the name to bind it to.
{==+==}
`self` 可以被导入作为当前 crate 的绑定，此时必须使用 `as` 子句来指定绑定的名称。
{==+==}


{==+==}
Three examples of `extern crate` declarations:
{==+==}
`extern crate` 声明的三个例子:
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
extern crate pcre;

extern crate std; // equivalent to: extern crate std as std;

extern crate std as ruststd; // linking to 'std' under another name
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
extern crate pcre;

extern crate std; // 等价于: extern crate std as std;

extern crate std as ruststd; // 以另一个名字链接到 'std' 。
```
{==+==}


{==+==}
When naming Rust crates, hyphens are disallowed. However, Cargo packages may
make use of them. In such case, when `Cargo.toml` doesn't specify a crate name,
Cargo will transparently replace `-` with `_` (Refer to [RFC 940] for more
details).
{==+==}
在 Rust 中，crate 的命名不允许使用连字符。
然而，Cargo 包允许使用连字符。
如果在 `Cargo.toml` 中没有指定 crate 名称，则 Cargo 会自动将 `-` 替换为 `_`，这个行为详见 [RFC 940] 。
{==+==}


{==+==}
Here is an example:
{==+==}
下面是一个例子:
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
// Importing the Cargo package hello-world
extern crate hello_world; // hyphen replaced with an underscore
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
// 导入Cargo package hello-world
extern crate hello_world; // 连字符替换为下划线
```
{==+==}


{==+==}
## Extern Prelude
{==+==}
## 外部 Prelude
{==+==}


{==+==}
This section has been moved to [Preludes — Extern Prelude](../names/preludes.md#extern-prelude).
<!-- this is to appease the linkchecker, will remove once other books are updated -->
{==+==}
本节已移至 [Preludes — Extern Prelude](../names/preludes.md#extern-prelude) 。
<!-- this is to appease the linkchecker, will remove once other books are updated -->
{==+==}


{==+==}
## Underscore Imports
{==+==}
## 下划线导入
{==+==}


{==+==}
An external crate dependency can be declared without binding its name in scope
by using an underscore with the form `extern crate foo as _`. This may be
useful for crates that only need to be linked, but are never referenced, and
will avoid being reported as unused.
{==+==}
使用 `extern crate foo as _` 的形式可以声明外部 crate 依赖，但不将其名称绑定到作用域中。
这对于只需要链接而从未被引用的 crate 可能很有用，避免它们被报告为未使用。
{==+==}


{==+==}
The [`macro_use` attribute] works as usual and imports the macro names
into the [`macro_use` prelude].
{==+==}
[`macro_use` 属性][`macro_use` attribute] 按照惯例导入宏名到 [`macro_use` 预导入][`macro_use` prelude]。
{==+==}


{==+==}
## The `no_link` attribute
{==+==}
## `no_link` 属性
{==+==}


{==+==}
The *`no_link` attribute* may be specified on an `extern crate` item to
prevent linking the crate into the output. This is commonly used to load a
crate to access only its macros.
{==+==}
*`no_link` 属性* 可在 `extern crate` 条目上指定，以防止将 crate 链接到输出中。
这通常用于加载一个 crate 仅用于访问其宏。
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[RFC 940]: https://github.com/rust-lang/rfcs/blob/master/text/0940-hyphens-considered-harmful.md
[`macro_use` attribute]: ../macros-by-example.md#the-macro_use-attribute
[extern prelude]: ../names/preludes.md#extern-prelude
[`macro_use` prelude]: ../names/preludes.md#macro_use-prelude
[`crate_name` attributes]: ../crates-and-source-files.md#the-crate_name-attribute
{==+==}

{==+==}


{==+==}
<script>
(function() {
    var fragments = {
        "#extern-prelude": "../names/preludes.html#extern-prelude",
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