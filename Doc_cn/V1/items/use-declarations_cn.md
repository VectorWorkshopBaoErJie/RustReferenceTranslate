{==+==}
# Use declarations
{==+==}
# Use 声明
{==+==}


{==+==}
> **<sup>Syntax:</sup>**\
> _UseDeclaration_ :\
> &nbsp;&nbsp; `use` _UseTree_ `;`
>
> _UseTree_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; ([_SimplePath_]<sup>?</sup> `::`)<sup>?</sup> `*`\
> &nbsp;&nbsp; | ([_SimplePath_]<sup>?</sup> `::`)<sup>?</sup> `{` (_UseTree_ ( `,`  _UseTree_ )<sup>\*</sup> `,`<sup>?</sup>)<sup>?</sup> `}`\
> &nbsp;&nbsp; | [_SimplePath_]&nbsp;( `as` ( [IDENTIFIER] | `_` ) )<sup>?</sup>
{==+==}
> **<sup>语法:</sup>**\
> _Use声明_ :\
> &nbsp;&nbsp; `use` _Use树_ `;`
>
> _Use树_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; ([_简单路径_][_SimplePath_]<sup>?</sup> `::`)<sup>?</sup> `*`\
> &nbsp;&nbsp; | ([_简单路径_][_SimplePath_]<sup>?</sup> `::`)<sup>?</sup> `{` (_Use树_ ( `,`  _Use树_ )<sup>\*</sup> `,`<sup>?</sup>)<sup>?</sup> `}`\
> &nbsp;&nbsp; | [_简单路径_][_SimplePath_]&nbsp;( `as` ( [标识符][IDENTIFIER] | `_` ) )<sup>?</sup>
{==+==}


{==+==}
A _use declaration_ creates one or more local name bindings synonymous with
some other [path]. Usually a `use` declaration is used to shorten the path
required to refer to a module item. These declarations may appear in [modules]
and [blocks], usually at the top.
{==+==}
_use 声明_ 会创建一个或多个本地的名称绑定，和某个其他路径同义。
通常，`use` 声明用于缩短引用模块条目所需的路径。
这些声明通常出现在 [模块][modules] 和 [块][blocks] 中，通常在顶部。
{==+==}


{==+==}
[path]: ../paths.md
[modules]: modules.md
[blocks]: ../expressions/block-expr.md
{==+==}

{==+==}


{==+==}
Use declarations support a number of convenient shortcuts:
{==+==}
Use 声明支持许多方便的快捷方式:
{==+==}


{==+==}
* Simultaneously binding a list of paths with a common prefix, using the
  glob-like brace syntax `use a::b::{c, d, e::f, g::h::i};`
* Simultaneously binding a list of paths with a common prefix and their common
  parent module, using the `self` keyword, such as `use a::b::{self, c, d::e};`
* Rebinding the target name as a new local name, using the syntax `use p::q::r
  as x;`. This can also be used with the last two features:
  `use a::b::{self as ab, c as abc}`.
* Binding all paths matching a given prefix, using the asterisk wildcard syntax
  `use a::b::*;`.
* Nesting groups of the previous features multiple times, such as
  `use a::b::{self as ab, c, d::{*, e::f}};`
{==+==}
* 使用类似通配符的花括号语法同时绑定具有相同前缀的路径列表，例如 `use a::b::{c, d, e::f, g::h::i};` 。
* 使用 `self` 关键字同时绑定具有相同前缀和共同父模块的路径列表，例如 `use a::b::{self, c, d::e};` 。
* 重新将目标名称绑定为新的本地名称，使用语法 `use p::q::r as x;` 。这也可以与前两个功能一起使用: `use a::b::{self as ab, c as abc}` 。
* 使用 星号通配符语法绑定与给定前缀匹配的所有路径，例如 `use a::b::*;` 。
* 多次嵌套前面的功能组合，例如 `use a::b::{self as ab, c, d::{*, e::f}};` 。
{==+==}


{==+==}
An example of `use` declarations:
{==+==}
以下关于 `use` 声明的例子:
{==+==}


{==+==}
```rust
use std::collections::hash_map::{self, HashMap};

fn foo<T>(_: T){}
fn bar(map1: HashMap<String, usize>, map2: hash_map::HashMap<String, usize>){}

fn main() {
    // use declarations can also exist inside of functions
    use std::option::Option::{Some, None};

    // Equivalent to 'foo(vec![std::option::Option::Some(1.0f64),
    // std::option::Option::None]);'
    foo(vec![Some(1.0f64), None]);

    // Both `hash_map` and `HashMap` are in scope.
    let map1 = HashMap::new();
    let map2 = hash_map::HashMap::new();
    bar(map1, map2);
}
```
{==+==}
```rust
use std::collections::hash_map::{self, HashMap};

fn foo<T>(_: T){}
fn bar(map1: HashMap<String, usize>, map2: hash_map::HashMap<String, usize>){}

fn main() {
    // use 声明也可以存在于函数中
    use std::option::Option::{Some, None};

    // 相当于 'foo(vec![std::option::Option::Some(1.0f64), std::option::Option::None]);'
    foo(vec![Some(1.0f64), None]);

    // `hash_map` 和 `HashMap` 都在作用域内。
    let map1 = HashMap::new();
    let map2 = hash_map::HashMap::new();
    bar(map1, map2);
}
```
{==+==}


{==+==}
## `use` Visibility
{==+==}
## `use` 可见性
{==+==}


{==+==}
Like items, `use` declarations are private to the containing module, by
default. Also like items, a `use` declaration can be public, if qualified by
the `pub` keyword. Such a `use` declaration serves to _re-export_ a name. A
public `use` declaration can therefore _redirect_ some public name to a
different target definition: even a definition with a private canonical path,
inside a different module. If a sequence of such redirections form a cycle or
cannot be resolved unambiguously, they represent a compile-time error.
{==+==}
和条目一样，默认情况下， `use` 声明只在其所在的模块中可见。
同样地，如果在 `use` 声明前加上 `pub` 关键字，那么 `use` 声明就是公共的。
这样的 `use` 声明就可以用来 _重新导出_ 一个名称。
因此，公共的 `use` 声明可以将某个公共名称重定向到一个不同的目标定义：甚至是定义在不同模块中的私有规范路径。
如果这样的重定向序列形成了一个循环或无法消除歧义，那么就会导致编译时错误。
{==+==}


{==+==}
An example of re-exporting:
{==+==}
一个重新导出的例子:
{==+==}


{==+==}
```rust
mod quux {
    pub use self::foo::{bar, baz};
    pub mod foo {
        pub fn bar() {}
        pub fn baz() {}
    }
}

fn main() {
    quux::bar();
    quux::baz();
}
```
{==+==}

{==+==}


{==+==}
In this example, the module `quux` re-exports two public names defined in
`foo`.
{==+==}
在这个例子中，模块 `quux` 重新导出了在 `foo` 中定义的两个公共名称。
{==+==}


{==+==}
## `use` Paths
{==+==}

{==+==}


{==+==}
> **Note**: This section is incomplete.
{==+==}
> **注意**: 本节内容不完整。
{==+==}


{==+==}
Some examples of what will and will not work for `use` items:
<!-- Note: This example works as-is in either 2015 or 2018. -->
{==+==}
以下是一些关于 `use` 声明能够和不能够正常工作的例子：
<!-- Note: This example works as-is in either 2015 or 2018. -->
{==+==}


{==+==}
```rust
# #![allow(unused_imports)]
use std::path::{self, Path, PathBuf};  // good: std is a crate name
use crate::foo::baz::foobaz;    // good: foo is at the root of the crate

mod foo {

    pub mod example {
        pub mod iter {}
    }

    use crate::foo::example::iter; // good: foo is at crate root
//  use example::iter;      // bad in 2015 edition: relative paths are not allowed without `self`; good in 2018 edition
    use self::baz::foobaz;  // good: self refers to module 'foo'
    use crate::foo::bar::foobar;   // good: foo is at crate root

    pub mod bar {
        pub fn foobar() { }
    }

    pub mod baz {
        use super::bar::foobar; // good: super refers to module 'foo'
        pub fn foobaz() { }
    }
}

fn main() {}
```
{==+==}
```rust
# #![allow(unused_imports)]
use std::path::{self, Path, PathBuf};  // good: std 是 crate 名称
use crate::foo::baz::foobaz;    // good: Foo 位于 crate 的根

mod foo {

    pub mod example {
        pub mod iter {}
    }

    use crate::foo::example::iter; // good: foo 位于 crate 的根
//  use example::iter;      // bad 在 2015 版次: 相对路径不允许没有 `self` ; 2018 版good
    use self::baz::foobaz;  // good: Self 引用模块 'foo'
    use crate::foo::bar::foobar;   // good: foo 位于 crate 的根

    pub mod bar {
        pub fn foobar() { }
    }

    pub mod baz {
        use super::bar::foobar; // good: Super 指的是模块 foo
        pub fn foobaz() { }
    }
}

fn main() {}
```
{==+==}


{==+==}
> **Edition Differences**: In the 2015 edition, `use` paths also allow
> accessing items in the crate root. Using the example above, the following
> `use` paths work in 2015 but not 2018:
>
{==+==}
> **版次差异**: 在2015版本中， `use` 路径还允许访问 crate 根中的条目。使用上面的例子，以下 `use` 路径在2015版中可以使用但在2018版中无法使用:
>
{==+==}


{==+==}
> ```rust,edition2015
> # mod foo {
> #     pub mod example { pub mod iter {} }
> #     pub mod baz { pub fn foobaz() {} }
> # }
> use foo::example::iter;
> use ::foo::baz::foobaz;
> # fn main() {}
> ```
>
{==+==}

{==+==}


{==+==}
> The 2015 edition does not allow use declarations to reference the [extern prelude].
> Thus [`extern crate`] declarations are still required in 2015 to
> reference an external crate in a use declaration. Beginning with the 2018
> edition, use declarations can specify an external crate dependency the same
> way `extern crate` can.
>
{==+==}
> 2015版不允许 `use` 声明引用 [extern 预定义][extern prelude] 。因此，在2015中，仍需要使用 [`extern crate`] 声明来引用 `use` 声明中的外部 crate。从 2018 版开始， `use` 声明可以像 `extern crate` 一样指定一个外部 crate 依赖。
>
{==+==}


{==+==}
> In the 2018 edition, if an in-scope item has the same name as an external
> crate, then `use` of that crate name requires a leading `::` to
> unambiguously select the crate name. This is to retain compatibility with
> potential future changes. <!-- uniform_paths future-proofing -->
>
{==+==}
> 在 2018 版本中，如果在作用域内存在与外部 crate 相同的名称，则使用该 crate 名称需要在前面加上 `::` 以明确选择 crate 名称。这是为了保留与未来潜在更改的兼容性。
> <!-- uniform_paths future-proofing -->
{==+==}


{==+==}
> ```rust
> // use std::fs; // Error, this is ambiguous.
> use ::std::fs;  // Imports from the `std` crate, not the module below.
> use self::std::fs as self_fs;  // Imports the module below.
>
> mod std {
>     pub mod fs {}
> }
> # fn main() {}
> ```
{==+==}
> ```rust
> // use std::fs; // Error, 有歧义
> use ::std::fs;  // 从 std crate 导入，而不是下面的模块。
> use self::std::fs as self_fs;  // 导入下面的模块。
>
> mod std {
>     pub mod fs {}
> }
> # fn main() {}
> ```
{==+==}


{==+==}
## Underscore Imports
{==+==}
## 下划线导入
{==+==}


{==+==}
Items can be imported without binding to a name by using an underscore with
the form `use path as _`. This is particularly useful to import a trait so
that its methods may be used without importing the trait's symbol, for example
if the trait's symbol may conflict with another symbol. Another example is to
link an external crate without importing its name.
{==+==}
条目可以通过在路径前加一个下划线的形式 `use path as _` 导入，而不必绑定到一个名字。
这种方法特别适用于导入 trait ，以便可以使用它的方法而不必导入该 trait 的符号，例如，如果该 trait 的符号可能与另一个符号发生冲突。
另一个例子是链接一个外部 crate ，而不必导入它的名字。
{==+==}


{==+==}
Asterisk glob imports will import items imported with `_` in their unnameable
form.
{==+==}
在Rust语言中，使用通配符 (`*`) 导入的内容将会包括使用下划线 (`_`) 导入但未被命名的内容。
这些未命名的内容可以被视为一个占位符，它们存在于模块的作用域中，但没有一个明确的名称来引用它们。
如果你在导入模块时使用了下划线来导入某些条目，这些条目将被视为 "无法命名的" 条目，只有使用通配符才能导入它们。
{==+==}


{==+==}
```rust
mod foo {
    pub trait Zoo {
        fn zoo(&self) {}
    }

    impl<T> Zoo for T {}
}

use self::foo::Zoo as _;
struct Zoo;  // Underscore import avoids name conflict with this item.

fn main() {
    let z = Zoo;
    z.zoo();
}
```
{==+==}
```rust
mod foo {
    pub trait Zoo {
        fn zoo(&self) {}
    }

    impl<T> Zoo for T {}
}

use self::foo::Zoo as _;
struct Zoo;  // 下划线导入避免了与此条目的名称冲突。

fn main() {
    let z = Zoo;
    z.zoo();
}
```
{==+==}


{==+==}
The unique, unnameable symbols are created after macro expansion so that
macros may safely emit multiple references to `_` imports. For example, the
following should not produce an error:
{==+==}
独特的、无法命名的符号是在宏展开后创建的，这样宏就可以安全地发出对 `_` 导入的多个引用。例如，下面的代码不应该出错:
{==+==}


{==+==}
```rust
macro_rules! m {
    ($item: item) => { $item $item }
}

m!(use std as _;);
// This expands to:
// use std as _;
// use std as _;
```
{==+==}
```rust
macro_rules! m {
    ($item: item) => { $item $item }
}

m!(use std as _;);
// 展开为:
// use std as _;
// use std as _;
```
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[_SimplePath_]: ../paths.md#simple-paths
[`extern crate`]: extern-crates.md
[extern prelude]: ../names/preludes.md#extern-prelude
[path qualifiers]: ../paths.md#path-qualifiers
{==+==}

{==+==}