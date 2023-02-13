{==+==}
# Derive
{==+==}
# 派生
{==+==}


{==+==}
The *`derive` attribute* allows new [items] to be automatically generated for
data structures. It uses the [_MetaListPaths_] syntax to specify a list of
traits to implement or paths to [derive macros] to process.
{==+==}
*` derive` 属性* 允许为数据结构自动生成新的 [条目][items] 。
它使用 [_MetaListPaths_] 语法来指定要实现的 trait 列表或要处理的 [派生宏][derive macros] 路径。
{==+==}


{==+==}
For example, the following will create an [`impl` item] for the
[`PartialEq`] and [`Clone`] traits for `Foo`, and the type parameter `T` will be
given the `PartialEq` or `Clone` constraints for the appropriate `impl`:
{==+==}
例如，下面将为 `Foo` 的 [`PartialEq`] 和 [`Clone`] trait 创建一个 [`impl` 条目][`impl` item] ，
类型参数 `T` 将被赋予适用的 `impl` 的 `PartialEq` 或 `Clone` 约束。
{==+==}


{==+==}
```rust
#[derive(PartialEq, Clone)]
struct Foo<T> {
    a: i32,
    b: T,
}
```
{==+==}

{==+==}


{==+==}
The generated `impl` for `PartialEq` is equivalent to
{==+==}
对于 `PartialEq` 所生成的 `impl` 等同于
{==+==}


{==+==}
```rust
# struct Foo<T> { a: i32, b: T }
impl<T: PartialEq> PartialEq for Foo<T> {
    fn eq(&self, other: &Foo<T>) -> bool {
        self.a == other.a && self.b == other.b
    }
}
```
{==+==}

{==+==}


{==+==}
You can implement `derive` for your own traits through [procedural macros].
{==+==}
你可以通过 [过程宏][procedural macros] 为你自己的 trait 实现 `derive` 。
{==+==}


{==+==}
## The `automatically_derived` attribute
{==+==}
## `automatically_derived` 属性
{==+==}


{==+==}
The *`automatically_derived` attribute* is automatically added to
[implementations] created by the `derive` attribute for built-in traits. It
has no direct effect, but it may be used by tools and diagnostic lints to
detect these automatically generated implementations.
{==+==}
 *`automatically_derived` 属性* 被自动添加到由 `derive` 属性为内置 trait 创建的 [实现][implementations] 中。
它没有直接作用，但可以被工具和诊断 lint 用来检测这些自动生成的实现。
{==+==}


{==+==}
[_MetaListPaths_]: ../attributes.md#meta-item-attribute-syntax
[`Clone`]: ../../std/clone/trait.Clone.html
[`PartialEq`]: ../../std/cmp/trait.PartialEq.html
[`impl` item]: ../items/implementations.md
[items]: ../items.md
[derive macros]: ../procedural-macros.md#derive-macros
[implementations]: ../items/implementations.md
[items]: ../items.md
[procedural macros]: ../procedural-macros.md#derive-macros
{==+==}

{==+==}