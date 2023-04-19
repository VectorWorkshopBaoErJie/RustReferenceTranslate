{==+==}
# Limits

The following [attributes] affect compile-time limits.

## The `recursion_limit` attribute

The *`recursion_limit` attribute* may be applied at the [crate] level to set the
maximum depth for potentially infinitely-recursive compile-time operations
like macro expansion or auto-dereference. It uses the [_MetaNameValueStr_]
syntax to specify the recursion depth.

> Note: The default in `rustc` is 128.
{==+==}
# 限制

下面的 [属性][attributes] 影响编译时的限制。

## `recursion_limit` 属性

*`recursion_limit` 属性* 可以在 [crate] 级别上应用，以设置潜在的无限递归编译时操作 (如宏展开或自动解引用) 的最大深度。它使用 [_元名称值字符串_][_MetaNameValueStr_] 语法来指定递归深度。

> 注意：`rustc` 中的默认值是 128。
{==+==}


{==+==}
```rust,compile_fail
#![recursion_limit = "4"]

macro_rules! a {
    () => { a!(1); };
    (1) => { a!(2); };
    (2) => { a!(3); };
    (3) => { a!(4); };
    (4) => { };
}

// This fails to expand because it requires a recursion depth greater than 4.
a!{}
```
{==+==}
```rust,compile_fail
#![recursion_limit = "4"]

macro_rules! a {
    () => { a!(1); };
    (1) => { a!(2); };
    (2) => { a!(3); };
    (3) => { a!(4); };
    (4) => { };
}

// 这段代码无法扩展，因为它需要超过 4 的递归深度。
a!{}
```
{==+==}


{==+==}
```rust,compile_fail
#![recursion_limit = "1"]

// This fails because it requires two recursive steps to auto-dereference.
(|_: &u8| {})(&&&1);
```
{==+==}
```rust,compile_fail
#![recursion_limit = "1"]

// 这个失败是因为它需要进行两个递归步骤来自动解引用。
(|_: &u8| {})(&&&1);
```
{==+==}


{==+==}
## The `type_length_limit` attribute

The *`type_length_limit` attribute* limits the maximum number of type
substitutions made when constructing a concrete type during monomorphization.
It is applied at the [crate] level, and uses the [_MetaNameValueStr_] syntax
to set the limit based on the number of type substitutions.

> Note: The default in `rustc` is 1048576.
{==+==}
## `type_length_limit` 属性

`type_length_limit` 属性限制了当进行单态化时，构建具体类型所做的最大类型替换数量。
它应用于 [crate] 级别，使用 [_元名称值字符串_][_MetaNameValueStr_] 语法设置限制，以基于类型替换数量设置限制。

> 注意：`rustc` 中的默认值为 1048576。
{==+==}


{==+==}
```rust,compile_fail
#![type_length_limit = "4"]

fn f<T>(x: T) {}

// This fails to compile because monomorphizing to
// `f::<((((i32,), i32), i32), i32)>` requires more than 4 type elements.
f(((((1,), 2), 3), 4));
```
{==+==}
```rust,compile_fail
#![type_length_limit = "4"]

fn f<T>(x: T) {}

// 编译失败，因为将 f::<((((i32,), i32), i32), i32)> 进行单态化需要超过 4 个类型元素。
f(((((1,), 2), 3), 4));
```
{==+==}


{==+==}
[_MetaNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[attributes]: ../attributes.md
[crate]: ../crates-and-source-files.md
{==+==}

{==+==}