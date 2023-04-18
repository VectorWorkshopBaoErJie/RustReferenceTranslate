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
切片是一个 [动态大小类型][dynamically sized type] ，表示对类型为 `T` 的元素序列的 '视图' 。切片类型的写法为 `[T]` 。

切片类型通常通过指针类型来使用。例如：

* `&[T]`：一个 '共享切片' ，通常称为 '切片' 。它不拥有它所指向的数据，而是借用它。
* `&mut [T]`：一个 '可变切片' 。它可变地借用它所指向的数据。
* `Box<[T]>`：一个 '装箱切片' 。

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
// 将一个堆分配的数组转换成一个切片
let boxed_array: Box<[i32]> = Box::new([1, 2, 3]);

// 从一个数组中创建一个 (共享的) 切片
let slice: &[i32] = &boxed_array[..];
```
{==+==}


{==+==}
All elements of slices are always initialized, and access to a slice is always
bounds-checked in safe methods and operators.
{==+==}
所有切片元素都是始终被初始化的，而且在安全的方法和操作中，访问切片时总是会进行边界检查。
{==+==}


{==+==}
[_Type_]: ../types.md#type-expressions
[dynamically sized type]: ../dynamically-sized-types.md
{==+==}

{==+==}