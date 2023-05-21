{==+==}
# Unsafety

Unsafe operations are those that can potentially violate the memory-safety
guarantees of Rust's static semantics.

The following language level features cannot be used in the safe subset of
Rust:

- Dereferencing a [raw pointer].
- Reading or writing a [mutable] or [external] static variable.
- Accessing a field of a [`union`], other than to assign to it.
- Calling an unsafe function (including an intrinsic or foreign function).
- Implementing an [unsafe trait].
{==+==}
# 不安全性

不安全操作是指那些可能违反 Rust 静态语义内存安全保证的操作。

以下语言级别的特性不能在 Rust 的安全子集中使用:

- 解引用 [裸指针][raw pointer] 。
- 读取或写入 [可变][mutable] 或 [外部][external] 静态变量。
- 访问 [`union`] 的字段，除了给它赋值之外。
- 调用不安全的函数 (包括内部函数或外部函数) 。
- 实现 [不安全 trait][unsafe trait] 。
{==+==}


{==+==}
[`union`]: items/unions.md
[mutable]: items/static-items.md#mutable-statics
[external]: items/external-blocks.md
[raw pointer]: types/pointer.md
[unsafe trait]: items/traits.md#unsafe-traits
{==+==}

{==+==}