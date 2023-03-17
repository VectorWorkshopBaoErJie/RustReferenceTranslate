{==+==}
# Array types
{==+==}
# 数组类型
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ArrayType_ :\
> &nbsp;&nbsp; `[` [_Type_] `;` [_Expression_] `]`
{==+==}

{==+==}


{==+==}
An array is a fixed-size sequence of `N` elements of type `T`. The array type
is written as `[T; N]`. The size is a [constant expression] that evaluates to a
[`usize`].

Examples:
{==+==}
数组是一种固定大小的类型为 `T` 的元素序列，数组类型的写法为 `[T; N]` 。
其大小是一个 [常量表达式][constant expression] ，值应该是 [`usize`] 类型。

例如:
{==+==}


{==+==}
```rust
// A stack-allocated array
let array: [i32; 3] = [1, 2, 3];

// A heap-allocated array, coerced to a slice
let boxed_array: Box<[i32]> = Box::new([1, 2, 3]);
```
{==+==}
```rust
// 一个栈上分配的数组
let array: [i32; 3] = [1, 2, 3];

// 一个堆上分配的数组，转换为一个切片类型
let boxed_array: Box<[i32]> = Box::new([1, 2, 3]);
```
{==+==}


{==+==}
All elements of arrays are always initialized, and access to an array is
always bounds-checked in safe methods and operators.

> Note: The [`Vec<T>`] standard library type provides a heap-allocated resizable
> array type.
{==+==}
数组的所有元素都被初始化，且在安全方法和运算符中访问数组时始终进行边界检查。

> 注意: 标准库类型 [`Vec<T>`] 提供了一种在堆上分配的可调整大小的数组类型。
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[_Type_]: ../types.md#type-expressions
[`Vec<T>`]: ../../std/vec/struct.Vec.html
[`usize`]: numeric.md#machine-dependent-integer-types
[constant expression]: ../const_eval.md#constant-expressions
{==+==}

{==+==}