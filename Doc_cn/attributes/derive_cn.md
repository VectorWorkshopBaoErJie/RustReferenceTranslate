{==+==}
# Derive
{==+==}
# 衍生
{==+==}


{==+==}
The *`derive` attribute* allows new [items] to be automatically generated for
data structures. It uses the [_MetaListPaths_] syntax to specify a list of
traits to implement or paths to [derive macros] to process.
{==+==}
*` derive` 属性* 允许自动生成数据结构的新 [条目][items] 。
它使用 [_MetaListPaths_] 语法来指定要实现的一组 trait 或要处理的 [衍生宏][derive macros] 的路径。
{==+==}


{==+==}
For example, the following will create an [`impl` item] for the
[`PartialEq`] and [`Clone`] traits for `Foo`, and the type parameter `T` will be
given the `PartialEq` or `Clone` constraints for the appropriate `impl`:
{==+==}
例如，以下代码将为 `Foo` 创建一个 [`impl` 条目][`impl` item] ，实现 [`PartialEq`] 和 [`Clone`] 两个 trait ，
而类型参数 `T` 将被赋予相应 `impl` 中的 `PartialEq` 或 `Clone` 约束。
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
自动生成的实现由 `derive` 属性自动添加了 `automatically_derived` 属性，它本身没有直接作用，但是可以被工具和诊断lint用来检测这些自动生成的实现。
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