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

{==+==}


{==+==}
An _`extern crate` declaration_ specifies a dependency on an external crate.
The external crate is then bound into the declaring scope as the [identifier]
provided in the `extern crate` declaration. Additionally, if the `extern
crate` appears in the crate root, then the crate name is also added to the
[extern prelude], making it automatically in scope in all modules. The `as`
clause can be used to bind the imported crate to a different name.
{==+==}
一个 _`extern crate` 声明_ 指定了对外部crate的依赖。
然后，外部crate被绑定到声明的作用域中，作为 `extern crate` 声明中提供的 [identifier]。
此外，如果 `extern crate` 出现在crate root中，那么crate的名称也会被添加到 [extern prelude] 中，使其自动进入所有模块的作用域。
`as` 子句可以用来将导入的crate绑定到一个不同的名称。
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
外部crate在编译时解析为一个特定的 `soname` ，运行时给链接器传递对该 `soname` 的链接请求，以便在运行时加载。
`soname` 在编译时通过扫描编译器的库路径来解析，并将提供的可选 `crate_name` 与编译时在外部crate上声明的 [`crate_name` attributes] 相匹配。
如果没有提供 `crate_name` ，则假定有一个默认的 `name` 属性，等同于在 `extern crate` 声明中给出的 [identifier] 标识符。
{==+==}


{==+==}
The `self` crate may be imported which creates a binding to the current crate.
In this case the `as` clause must be used to specify the name to bind it to.
{==+==}
``self` crate 可以被导入，从而创建一个与当前 crate 的绑定。
在这种情况下，必须使用 `as` 子句来指定要绑定的名称。
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
当给 Rust crate命名时，不允许使用连字符。然而，Cargo包可以使用它们。
在这种情况下，当 `Cargo.toml` 没有指定crate的名称时，Cargo 将明显的用 `_` 替换 `-` (更多细节参考[RFC 940]) 。
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
可以通过使用下划线的形式 `extern crate foo as _` 来声明一个外部的crate依赖，而不用在作用域内绑定其名称。
这对于那些只需要被链接，但从未被引用的crate可能很有用，而且可以避免报告未使用。
{==+==}


{==+==}
The [`macro_use` attribute] works as usual and imports the macro names
into the [`macro_use` prelude].
{==+==}
[`macro_use` attribute] 和常规一样生效，将宏名称导入到 [`macro_use` prelude] 。 
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
*`no_link` 属性* 可以被指定在一个 `extern crate` 条目上，以防止将crate链接到输出中。
这通常用于加载crate，只访问其宏。
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