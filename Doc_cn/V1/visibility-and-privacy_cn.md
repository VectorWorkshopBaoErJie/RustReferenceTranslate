{==+==}
# Visibility and Privacy
{==+==}
# 可见性和私有性
{==+==}


{==+==}
> **<sup>Syntax<sup>**\
> _Visibility_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `pub`\
> &nbsp;&nbsp; | `pub` `(` `crate` `)`\
> &nbsp;&nbsp; | `pub` `(` `self` `)`\
> &nbsp;&nbsp; | `pub` `(` `super` `)`\
> &nbsp;&nbsp; | `pub` `(` `in` [_SimplePath_] `)`
{==+==}
> **<sup>语法<sup>**\
> _可见性_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `pub`\
> &nbsp;&nbsp; | `pub` `(` `crate` `)`\
> &nbsp;&nbsp; | `pub` `(` `self` `)`\
> &nbsp;&nbsp; | `pub` `(` `super` `)`\
> &nbsp;&nbsp; | `pub` `(` `in` [_SimplePath_] `)`
{==+==}


{==+==}
These two terms are often used interchangeably, and what they are attempting to
convey is the answer to the question "Can this item be used at this location?"

Rust's name resolution operates on a global hierarchy of namespaces. Each level
in the hierarchy can be thought of as some item. The items are one of those
mentioned above, but also include external crates. Declaring or defining a new
module can be thought of as inserting a new tree into the hierarchy at the
location of the definition.

To control whether interfaces can be used across modules, Rust checks each use
of an item to see whether it should be allowed or not. This is where privacy
warnings are generated, or otherwise "you used a private item of another module
and weren't allowed to."

By default, everything is *private*, with two exceptions: Associated
items in a `pub` Trait are public by default; Enum variants
in a `pub` enum are also public by default. When an item is declared as `pub`,
it can be thought of as being accessible to the outside world. For example:
{==+==}
这两个术语经常被交替使用，它们试图表达 "此条目是否可在此位置使用？" 的答案。

Rust 的名称解析是在全局层次结构的命名空间中运行的。层次结构中的每个级别都可以被视为某个条目。
这些条目是上面提到的其中之一，也包括外部 crate 。声明或定义新模块可以被视为在定义位置将一个新树插入层次结构中。

为了控制接口是否可以跨模块使用， Rust 检查每个条目的使用情况，以确定是否允许使用。
从而会产生隐私警告，会有 "你使用了另一个模块的私有条目，但不允许" 的提示。

默认情况下，所有内容都是 *私有的* ，有两个例外: 在 `pub` Trait 中的关联条目默认是公开的；在 `pub` 枚举中的枚举变量也默认是公开的。
当一个条目被声明为 `pub` 时，可以将其视为对外界可访问的。例如:
{==+==}


{==+==}
```rust
# fn main() {}
// Declare a private struct
struct Foo;

// Declare a public struct with a private field
pub struct Bar {
    field: i32,
}

// Declare a public enum with two public variants
pub enum State {
    PubliclyAccessibleState,
    PubliclyAccessibleState2,
}
```
{==+==}
```rust
# fn main() {}
// 声明一个私有结构体
struct Foo;

// 声明一个公开结构体，其中有一个私有字段
pub struct Bar {
    field: i32,
}

// 声明一个公开枚举类型，其中有两个公开变体
pub enum State {
    PubliclyAccessibleState,
    PubliclyAccessibleState2,
}
```
{==+==}


{==+==}
With the notion of an item being either public or private, Rust allows item
accesses in two cases:

1. If an item is public, then it can be accessed externally from some module
   `m` if you can access all the item's ancestor modules from `m`. You can
   also potentially be able to name the item through re-exports. See below.
2. If an item is private, it may be accessed by the current module and its
   descendants.

These two cases are surprisingly powerful for creating module hierarchies
exposing public APIs while hiding internal implementation details. To help
explain, here's a few use cases and what they would entail:

* A library developer needs to expose functionality to crates which link
  against their library. As a consequence of the first case, this means that
  anything which is usable externally must be `pub` from the root down to the
  destination item. Any private item in the chain will disallow external
  accesses.

* A crate needs a global available "helper module" to itself, but it doesn't
  want to expose the helper module as a public API. To accomplish this, the
  root of the crate's hierarchy would have a private module which then
  internally has a "public API". Because the entire crate is a descendant of
  the root, then the entire local crate can access this private module through
  the second case.

* When writing unit tests for a module, it's often a common idiom to have an
  immediate child of the module to-be-tested named `mod test`. This module
  could access any items of the parent module through the second case, meaning
  that internal implementation details could also be seamlessly tested from the
  child module.
{==+==}
在 Rust 中，通过将条目定义为公开或私有，允许在以下两种情况下访问条目：

1. 如果条目是公开的，则可以从一些模块 `m` 外部访问它，如果你可以从 `m` 访问所有条目的祖先模块，则还可以通过重新导出的方式命名该条目。
2. 如果条目是私有的，则当前模块及其子代可以访问它。

这两种情况对于创建公开 API 的模块层次结构并隐藏内部实现细节非常有用。以下是一些用例及其含义：

* 库开发人员需要向链接其库的 crate 公开功能。作为第一种情况的结果，这意味着任何可在外部使用的内容必须从根到目标条目都是 `pub` 的。链中的任何私有条目都将禁止外部访问。
* 一个 crate 需要一个仅对自己可用的全局 "帮助模块" ，但它不想将该模块公开为公开 API 。为此，crate 层次结构的根将具有一个私有模块，该模块内部具有 "公开 API" 。由于整个 crate 是根的子代，因此整个本地 crate 可以通过第二种情况访问此私有模块。
* 在为模块编写单元测试时，通常的惯用语法是让待测试的模块的直接子条目命名为 `mod test`。此模块可以通过第二种情况访问父模块的任何条目，从而可以轻松测试内部实现细节。
{==+==}


{==+==}
In the second case, it mentions that a private item "can be accessed" by the
current module and its descendants, but the exact meaning of accessing an item
depends on what the item is. Accessing a module, for example, would mean
looking inside of it (to import more items). On the other hand, accessing a
function would mean that it is invoked. Additionally, path expressions and
import statements are considered to access an item in the sense that the
import/expression is only valid if the destination is in the current visibility
scope.

Here's an example of a program which exemplifies the three cases outlined
above:
{==+==}
在第二种情况中，它提到了私有条目可以被当前模块和其子代模块 "访问" ，但是访问条目的确切含义取决于该条目是什么。
例如，访问模块将意味着查看其中的内容 (以导入更多条目) 。另一方面，访问函数将意味着调用它。
此外，路径表达式和导入语句被认为是访问条目，因为只有在目标在当前可见范围内时，导入/表达式才是有效的。

下面是一个程序示例，说明了上述三种情况:
{==+==}


{==+==}
```rust
// This module is private, meaning that no external crate can access this
// module. Because it is private at the root of this current crate, however, any
// module in the crate may access any publicly visible item in this module.
mod crate_helper_module {

    // This function can be used by anything in the current crate
    pub fn crate_helper() {}

    // This function *cannot* be used by anything else in the crate. It is not
    // publicly visible outside of the `crate_helper_module`, so only this
    // current module and its descendants may access it.
    fn implementation_detail() {}
}

// This function is "public to the root" meaning that it's available to external
// crates linking against this one.
pub fn public_api() {}

// Similarly to 'public_api', this module is public so external crates may look
// inside of it.
pub mod submodule {
    use crate::crate_helper_module;

    pub fn my_method() {
        // Any item in the local crate may invoke the helper module's public
        // interface through a combination of the two rules above.
        crate_helper_module::crate_helper();
    }

    // This function is hidden to any module which is not a descendant of
    // `submodule`
    fn my_implementation() {}

    #[cfg(test)]
    mod test {

        #[test]
        fn test_my_implementation() {
            // Because this module is a descendant of `submodule`, it's allowed
            // to access private items inside of `submodule` without a privacy
            // violation.
            super::my_implementation();
        }
    }
}

# fn main() {}
```
{==+==}
```rust
// 这个模块是私有的，意味着没有外部 crate 可以访问这个模块。然而，因为它在当前 crate 的根目录下是私有的，所以任何在 crate 中的模块都可以访问这个模块中任何公开可见的条目。
mod crate_helper_module {

    // 这个函数可以被当前 crate 中的任何东西使用
    pub fn crate_helper() {}

    // 这个函数 *不能* 被 crate 中的其他任何东西使用。它在 `crate_helper_module` 之外不可公开访问，因此只有这个当前模块及其子代可以访问它。
    fn implementation_detail() {}
}

// 这个函数是 "对根可见的公开"，这意味着它可以在链接到这个 crate 的外部 crate 中使用。
pub fn public_api() {}

// 类似于 'public_api'，这个模块是公开的，因此外部 crate 可以查看其内部。
pub mod submodule {
    use crate::crate_helper_module;

    pub fn my_method() {
        // 通过上述两条规则的组合，本地 crate 中的任何条目都可以调用辅助模块的公开接口。
        crate_helper_module::crate_helper();
    }

    // 这个函数对于不是 `submodule` 的子孙模块是隐藏的。
    fn my_implementation() {}

    #[cfg(test)]
    mod test {

        #[test]
        fn test_my_implementation() {
            // 因为这个模块是 `submodule` 的子孙模块，所以它允许访问 `submodule` 中的私有条目而不会违反隐私规定。
            super::my_implementation();
        }
    }
}

# fn main() {}
```
{==+==}


{==+==}
For a Rust program to pass the privacy checking pass, all paths must be valid
accesses given the two rules above. This includes all use statements,
expressions, types, etc.

## `pub(in path)`, `pub(crate)`, `pub(super)`, and `pub(self)`

In addition to public and private, Rust allows users to declare an item as
visible only within a given scope. The rules for `pub` restrictions are as
follows:
- `pub(in path)` makes an item visible within the provided `path`. `path` must
be an ancestor module of the item whose visibility is being declared.
- `pub(crate)` makes an item visible within the current crate.
- `pub(super)` makes an item visible to the parent module. This is equivalent
  to `pub(in super)`.
- `pub(self)` makes an item visible to the current module. This is equivalent
to `pub(in self)` or not using `pub` at all.

> **Edition Differences**: Starting with the 2018 edition, paths for
> `pub(in path)` must start with `crate`, `self`, or `super`. The 2015 edition
> may also use paths starting with `::` or modules from the crate root.

Here's an example:
{==+==}
为了使 Rust 程序通过隐私检查，所有路径必须根据上述两条规则进行有效访问。这包括所有的 use 语句、表达式、类型等。

## `pub(in path)` , `pub(crate)` , `pub(super)` , 和 `pub(self)` 

除了 '公开' 和 '私有' 之外， Rust 还允许用户将一个条目声明为仅在给定范围内可见。 `pub` 限制的规则如下：
- `pub(in path)` 使得一个条目在提供的 `path` 中可见。 `path` 必须是正在声明其可见性的条目的祖先模块。
- `pub(crate)` 使得一个条目在当前 crate 内可见。
- `pub(super)` 使得一个条目对其父模块可见。这等价于 `pub(in super)` 。
- `pub(self)` 使得一个条目对当前模块可见。这等价于 `pub(in self)` 或者不使用 `pub` 。

> **版本差异**: 从 2018 版开始， `pub(in path)` 的路径必须以 `crate` 、 `self` 或 `super` 开头。 2015 版也可以使用以 `::` 开头的路径或来自 crate 根的模块。

这是一个例子：
{==+==}


{==+==}
```rust,edition2015
pub mod outer_mod {
    pub mod inner_mod {
        // This function is visible within `outer_mod`
        pub(in crate::outer_mod) fn outer_mod_visible_fn() {}
        // Same as above, this is only valid in the 2015 edition.
        pub(in outer_mod) fn outer_mod_visible_fn_2015() {}

        // This function is visible to the entire crate
        pub(crate) fn crate_visible_fn() {}

        // This function is visible within `outer_mod`
        pub(super) fn super_mod_visible_fn() {
            // This function is visible since we're in the same `mod`
            inner_mod_visible_fn();
        }

        // This function is visible only within `inner_mod`,
        // which is the same as leaving it private.
        pub(self) fn inner_mod_visible_fn() {}
    }
    pub fn foo() {
        inner_mod::outer_mod_visible_fn();
        inner_mod::crate_visible_fn();
        inner_mod::super_mod_visible_fn();

        // This function is no longer visible since we're outside of `inner_mod`
        // Error! `inner_mod_visible_fn` is private
        //inner_mod::inner_mod_visible_fn();
    }
}

fn bar() {
    // This function is still visible since we're in the same crate
    outer_mod::inner_mod::crate_visible_fn();

    // This function is no longer visible since we're outside of `outer_mod`
    // Error! `super_mod_visible_fn` is private
    //outer_mod::inner_mod::super_mod_visible_fn();

    // This function is no longer visible since we're outside of `outer_mod`
    // Error! `outer_mod_visible_fn` is private
    //outer_mod::inner_mod::outer_mod_visible_fn();

    outer_mod::foo();
}

fn main() { bar() }
```
{==+==}
```rust,edition2015
pub mod outer_mod {
    pub mod inner_mod {
        // 此函数在 `outer_mod` 中可见
        pub(in crate::outer_mod) fn outer_mod_visible_fn() {}
        // 与上面的相同，在 2015 版中仅适用。
        pub(in outer_mod) fn outer_mod_visible_fn_2015() {}

        // 此函数在整个 crate 中可见
        pub(crate) fn crate_visible_fn() {}

        // 此函数在 `super` 中可见
        pub(super) fn super_mod_visible_fn() {
            // 因为在同一 `mod` 中，所以此函数可见
            inner_mod_visible_fn();
        }

        // 此函数仅在 `inner_mod` 中可见，
        // 这等同于将其声明为 private。
        pub(self) fn inner_mod_visible_fn() {}
    }
    pub fn foo() {
        inner_mod::outer_mod_visible_fn();
        inner_mod::crate_visible_fn();
        inner_mod::super_mod_visible_fn();

        // 由于已经在 `inner_mod` 的外部，因此此函数不再可见。
        // 错误！ `inner_mod_visible_fn` 是私有的
        //inner_mod::inner_mod_visible_fn();
    }
}

fn bar() {
    // 因为我们在同一 crate 中，所以此函数仍然可见。
    outer_mod::inner_mod::crate_visible_fn();

    // 由于我们在 `outer_mod` 的外部，因此此函数不再可见。
    // 错误！ `super_mod_visible_fn` 是私有的
    //outer_mod::inner_mod::super_mod_visible_fn();

    // 由于我们在 `outer_mod` 的外部，因此此函数不再可见。
    // 错误！ `outer_mod_visible_fn` 是私有的
    //outer_mod::inner_mod::outer_mod_visible_fn();

    outer_mod::foo();
}

fn main() { bar() }
```
{==+==}


{==+==}
> **Note:** This syntax only adds another restriction to the visibility of an
> item. It does not guarantee that the item is visible within all parts of the
> specified scope. To access an item, all of its parent items up to the
> current scope must still be visible as well.

## Re-exporting and Visibility

Rust allows publicly re-exporting items through a `pub use` directive. Because
this is a public directive, this allows the item to be used in the current
module through the rules above. It essentially allows public access into the
re-exported item. For example, this program is valid:
{==+==}
> **注意:** 这种语法只是增加了一个对条目可见性的限制，它并不保证该条目在指定范围内的所有部分都可见。
要访问一个条目，它的所有父级条目直到当前范围仍然必须可见。

## 重新导出和可见性

Rust 允许通过 `pub use` 公开且重新导出条目。因为这是一个使其公开的指令，从而允许通过上面的规则在当前模块中使用该条目。
这实际上允许了对重新导出的条目的公开访问。例如，这个程序是有效的：
{==+==}


{==+==}
```rust
pub use self::implementation::api;

mod implementation {
    pub mod api {
        pub fn f() {}
    }
}

# fn main() {}
```
{==+==}

{==+==}


{==+==}
This means that any external crate referencing `implementation::api::f` would
receive a privacy violation, while the path `api::f` would be allowed.

When re-exporting a private item, it can be thought of as allowing the "privacy
chain" being short-circuited through the reexport instead of passing through
the namespace hierarchy as it normally would.
{==+==}
这意味着，外部 crate 通过 `implementation::api::f` 引用将违反私有性，而允许以路径 `api::f` 引用。

当重新导出一个私有条目时，可以将其视为通过重新导出 "私有链条" 的快捷路径，而不是像通常一样通过命名空间层次结构传递。
{==+==}


{==+==}
[_SimplePath_]: paths.md#simple-paths
{==+==}

{==+==}