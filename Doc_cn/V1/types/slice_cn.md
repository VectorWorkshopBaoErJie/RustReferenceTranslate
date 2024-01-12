{==+==}
# Slice types
{==+==}

{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _SliceType_ :\
> &nbsp;&nbsp; `[` [_Type_] `]`
{==+==}
> **<sup>语法</sup>**\
> _切片类型_ :\
> &nbsp;&nbsp; `[` [_类型_][_Type_] `]`
{==+==}


{==+==}
A slice is a [dynamically sized type] representing a 'view' into a sequence of
elements of type `T`. The slice type is written as `[T]`.

Slice types are generally used through pointer types. For example:

* `&[T]`: a 'shared slice', often just called a 'slice'. It doesn't own the
  data it points to; it borrows it.
* `&mut [T]`: a 'mutable slice'. It mutably borrows the data it points to.
* `Box<[T]>`: a 'boxed slice'

Examples:
{==+==}
切片是 [动态大小类型][dynamically sized type] ，表示对类型为 `T` 的元素序列的 '视图' 。
切片类型书写为 `[T]` 。

切片类型通常通过指针类型来使用。例如：

* `&[T]`：为 '共享切片' ，通常称 '切片' ，不拥有所指向的数据，为借用。
* `&mut [T]`：为 '可变切片' 。该类型可变借用所指向的数据。
* `Box<[T]>`：为 '装箱切片' 。

例如:
{==+==}


{==+==}
```rust
// A heap-allocated array, coerced to a slice
let boxed_array: Box<[i32]> = Box::new([1, 2, 3]);

// A (shared) slice into an array
let slice: &[i32] = &boxed_array[..];
```
{==+==}
```rust
// 将一个堆分配的数组转换为切片
let boxed_array: Box<[i32]> = Box::new([1, 2, 3]);

// 从一个数组中创建共享切片
let slice: &[i32] = &boxed_array[..];
```
{==+==}


{==+==}
All elements of slices are always initialized, and access to a slice is always
bounds-checked in safe methods and operators.
{==+==}
所有切片元素始终被初始化，访问切片时总是会进行边界检查，方法和操作是安全的。
{==+==}


{==+==}
[_Type_]: ../types.md#type-expressions
[dynamically sized type]: ../dynamically-sized-types.md
{==+==}

{==+==}